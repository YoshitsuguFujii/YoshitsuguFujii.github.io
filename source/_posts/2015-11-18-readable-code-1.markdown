---
layout: post
title: "リーダブルコードを意識しながら普段気をつけていること(1)"
date: 2015-11-18 11:33:53 +0900
comments: true
categories: [ruby,readable code]
---

初のシリーズもの。不定期開催します。  
普段コードを書く時にリーダブルなコードを書く時に気をつけていることです。  
思いついたら書いていきます。  
  
第１回目は  
  
**コンテキストを作る**

<!-- more -->

例えばcsvアップロード機能・ダウンロード機能を作ったときに以下のようなメソッドがあるとします。

```ruby
class Hoge
  def download
    header
  end

  def upload
  end

  private
  def header
  end
end
```
  
ここでのheaderメソッドはcsvダウンロード時にヘッダを作るメソッドとします。  
  
初めてソースを見た人はheaderメソッドを見ただけではそれがアップロードに使われるのかダウンロードに使われるのかわかりません。  
downloadメソッドもしくはuploadメソッドを読んではじめてどちらで使えるかわかるようになるかと思います。  
  
これを両方のメソッドを読まずにheaderメソッドがどこで必要とされているかを一発でわかるようにしたいと思います。  
  
```ruby
class Hoge
  module Download
    def download
      header
    end

    def header
    end
  end

  module Upload
    def upload
    end
  end

  include Download
  include Upload
end
```
  
機能毎(あるいは関心ごと)にメソッドをmoduleを使って閉じ込めることで、  
headerメソッドがどこで必要となるか一発でわかるようになりました。  
  
またdownloadに不具合があった場合にはDownloadモジュールだけを読めばいいようになったため、読まなければいけないソースは格段に減ってコードリーディングする人の負担もだいぶ軽くなったのではないでしょうか？
  
今回は簡単な例ということでclassの中にmoduleを書いてしまいましたが、  
moduleの処理が大きくなる場合にはもちろんファイル分割 & 適切にクラス分割して記述するべきだと思います。  
