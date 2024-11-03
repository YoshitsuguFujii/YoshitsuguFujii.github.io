---
layout: post
title: "Valueオブジェクトパターン"
date: 2024-11-03 14:29:30 +0900
comments: true
categories: [ruby,ruby on rails, デザインパターン]
---

メリークリスマス！（はやい）。

家族と牛丼屋さんで、

昔は並だと物足りなかったのに、最近は持て余すよなぁ！

と笑顔で言ったら、

おじいちゃんになったんじゃない？

と言われて静かにショックをうけて黙ってしまったシニアなフリーランスエンジニアの藤井です。

この記事は、[Ruby on Rails Advent Calendar 2024 Advent Calendar 2024](https://qiita.com/advent-calendar/2024/ruby-on-rails) の1日目です。

すいません。偉そうな名前をつけていますが、ActiveRecordで値オブジェクトを使うことで、より見やすくわかりやすく、責務を適切に閉じ込めて疎結合にし、コードをシンプルにするにあたって有意義だったと感じた実装方法です。

もしすでに既出だったりしたらそっと教えて下さい。

<!-- more -->  

<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>    
<ins class="adsbygoogle"    
     style="display:block; text-align:center;"    
     data-ad-layout="in-article"    
     data-ad-format="fluid"    
     data-ad-client="ca-pub-7039502723411845"    
     data-ad-slot="8206045005"></ins>    
<script>    
     (adsbygoogle = window.adsbygoogle || []).push({});    
</script>   

Railsの一般的なコードを見ると、どこでもhashを持ち回して、キーアクセスで欲しいデータにアクセスして取り出すという実装が多い気がしていて、hashの空中戦みたいな、ある時点でその中身がどうなっているのかコード上でわからないのがだいぶ辛いと感じる時があります(処理を全部追うか、デバッグでとめて中身を見るか)

それを顕著に感じるのはテーブルにjson型を使っているときです。

中身はそのままhashだったりするのですが、中身ぱぱっと想像できます？  
DBの値を見てもイマイチなんてこと多いんじゃないでしょうか。

そんなjson型のカラムに適用すると真価を発揮するパターンなんですが、今回はよりシンプルな数量というものに適用してみたいと思います(json型はまた別の機会に)

そもそもなんでこの実装にしようと思ったかというと、Goとかで実装すると構造体に構造体を埋めて生成するのが当たり前ですよね？

```go
// 住所の値オブジェクト
type Address struct {
    Street string
    City   string
    Zip    string
}

// Person構造体
type Person struct {
    Name    string
    Age     int
    Address Address // Address構造体を埋め込む
}
```

Person型に住所型の責務と実装がにじみでることはないし、Personが住所にZipが必須かチェックするということも発生しません。ロジックが綺麗にカプセル化されていて、とても整理されていると感じます。住所の持ち方を変えたとしてもその影響がPersonに影響を与えることもありません。

これRailsでも使えない？と思ったのがはじまりです。


では実際に見ていきましょう。

サンプルコードは以下のバージョンで動かしています。

Ruby 3.3.5  
Rails 7.2.2

また見やすくするために[bootstrap_form](https://github.com/bootstrap-ruby/bootstrap_form)を使っています。

購入モデルを作成します。購入モデルはユーザー名と数量を持ちます。

数量はマイナス値でないこと、最大値は99というルールを持ちます。

まずは値オブジェクトとして数量を定義します。

DDDの世界で開発している人から見ると、数量はただの数値ではありません。数値は数値で数量は数量です。という言葉が聞こえてきそうです。  
数量がマイナス値でないこと、99を超える数値でないことを数量自身が管理できると良さそうです。

```ruby
module Values
  class Quantity
    include ActiveModel::Model

    attr_reader :number

    validates :number, presence: true
    validates :number, numericality: {
      only_integer: true,
      greater_than: 0,
      less_than_or_equal_to: 99
    }

    validate :hogehoge

    def initialize(number)
      @number = number
    end

    def to_i
      @number.to_i
    end

    def hogehoge
      # カスタムバリデーションも使えるぜというだけのメソッド
    end
  end
end
```

値オブジェクトの中に数量に関するロジックが閉じ込められています。

次にmodelで値オブジェクトをつかえるようにするためのserializerの定義です。

```ruby
module Serializers
  class QuantitySerializer
    # Rubyオブジェクトを変換
    def self.dump(quantity)
      if quantity.is_a?(Hash)
        Values::Quantity.new(quantity[:number])
      else
        quantity
      end.to_i
    end

    # Rubyオブジェクトに変換
    def self.load(quantity)
      Values::Quantity.new(quantity)
    end
  end
end
```

データベースから取り出した時に値オブジェクトに変換し、データベースに保存する時に数値に戻しています。

最後にmodelです

```ruby
class Purchase < ApplicationRecord
  serialize :quantity, coder: Serializers::QuantitySerializer

  validate :validate_quantity

  def validate_quantity
    return if self.quantity.valid?

    errors.add(:base, "数量にエラーがあります")
  end
end
```

さすがに透過的に値オブジェクトのvalidationは実行してくれないので、マニュアルで実行する必要があります。

上記だけで数量という値オブジェクトをPurchaseモデルに埋め込むことができます。

見て分かる通り、数量のロジックがPurchaseモデルに現れることはありません。責務で綺麗に分割され、ロジックがカプセル化されているのがわかると思います。
そしてserializeの行を消せば数量ロジックが完全にデタッチできます。違う数量の値オブジェクトを作って差し替えることも可能です。完全に疎結合です。

そして他のモデルで数量を使いたい場合には、Purchaseモデルの中身をそっくりapp/models/concerns配下のmoduleに入れてそれを使いたいモデルでimportするようにすれば、他のモデルでも簡単に使い回しが可能です。

さらにその場合、数量ロジックが変わったとしても各モデルに手をいれるのではなくて、数量クラスのロジックに手をいれるだけで自動的に全クラスに適用されます(影響範囲が閉じられている)

では実際に動かしてみましょう。

コントローラーとviewのformのコードものせます。

コントローラー(新規作成だけ)

```ruby
class PurchasesController < ApplicationController
  def new
    @purchase = Purchase.new
  end

  def create
    @purchase = Purchase.new(purchase_params)

    if @purchase.save
      redirect_to @purchase, notice: "Purchase was successfully created."
    else
      render :new, status: :unprocessable_entity 
    end
  end

  private

  def purchase_params
    params.require(:purchase).permit(:username, quantity: :number)
  end
end
```

form

```ruby
<%= bootstrap_form_for purchase do |f| %>
  <% if purchase.errors.any? %>
    <div class="alert alert-danger">
      <h4><%= pluralize(purchase.errors.count, "error") %> prohibited this purchase from being saved:</h4>
      <ul>
        <% purchase.errors.full_messages.each do |message| %>
          <li><%= message %></li>
        <% end %>
      </ul>
    </div>
  <% end %>

  <div class="row">
    <div class="ms-3 mb-3 col-4">
      <%= f.text_field :username, label: "Username", class: "form-control" %>
    </div>
  </div>

  <div class="row">
    <div class="ms-3 mb-3 col-4">
      <%= f.fields_for :quantity, f.object.quantity do |fq| %>
        <%= fq.number_field :number, label: "Quantity", class: "form-control" %>
      <% end %>
    </div>
  </div>

  <div class="mt-3">
    <%= f.submit class: "btn btn-primary" %>
  </div>
<% end %>
```

fields_forを使わずに書くと実はもうちょっとserializerとかパラメータの取り回しとかをシンプルに書けたりするんですが、こう書くとエラーオブジェクトを透過的に伝播させれるため使っています。

実際にvalidationエラーをおこした画面がこちらです。

<img src="/images/blog/20241103/error.png">  

値オブジェクトのエラーも特別な記述なくしっかりとformでレンダリングされています。

validationエラーを起こさないで保存に成功した場合は、DBに数値のみが入っているはずです。

いかがでしたでしょうか？

ActiveRecordのモデルに値オブジェクトを適用することで、ロジックが読みやすく、わかりやすくなり、ロジックはカプセル化されて影響範囲が読みやすくなり、責務は明確になり、疎結合になることにより、よりオブジェクト指向に近づけられたのではないでしょうか？

Valueオブジェクトパターンの紹介でした。

次回書く気力があればこの回の焼き回しになりそうですが、json編をお届けしようと思います。

今回作成したコードは[こちら](https://github.com/YoshitsuguFujii/value_pattern)にあります
