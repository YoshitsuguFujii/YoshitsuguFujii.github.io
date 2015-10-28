---
layout: post
title: "i18nで例えば大人と子供で文言を変えたい場合"
date: 2015-10-28 20:41:26 +0900
comments: true
categories: [ruby,ruby on rails]
---

  
条件に応じて文言を変えたい場合があると思います。  
例えば大人と子供で文言を変えたい場合とかですかね。  
ログインユーザが大人の場合は大人料金と表示し、子供の場合は子供料金と表示するようにします。  
  
```erb
<% if current_user.adult? %>
  <span>大人料金</span>
<% else %>
  <span>子供料金</span>
<% end %>
```
  
これが一箇所ならいいですが、たくさん出現するとつらいですね。  
helperにまとめたりするのでしょうか。  
こういった文言だけのif文分岐がviewに大量に出現するとコード量も増え非常に見ずらく、またhelperに乱立してしまうのは避けたいですね。  
  
そこで自動でi18nの読み込み先を条件に応じて読み替えたいと思います  

ついでにjpmobile使用してviewをpcとスマフォで切り替えている場合に  
lazy lookup記法時にスマフォページでsmart_phoneがついているlocaleを探してしまうのを防ぎます。 
  
<!-- more -->
  

config/initializers/switch_translation_helper.rbを作ります。
  
```ruby
module SwitchTranslationHelper
  def translate(key, options = {})
    prefix = current_user.adult? ? "adult" : "child"
    if request.smart_phone?
      if key.start_with?(".") && @virtual_path.end_with?("_smart_phone")
        @virtual_path.gsub!("_smart_phone", "") # jpmobileが付与する_smart_phoneを削除
      end
    end

    if prefix.present?
      key = scope_key_by_partial(key)
      key = "#{prefix}.#{key}" # 先頭にprefixを差し込む 
    end

    super(key, options)
  end
end

(A)
module ActionView
  module Helpers
    module TranslationHelper
      prepend SwitchTranslationHelper
      alias :t :translate
    end
  end
end
```
  
上記の(A)でActionView::Helpers::TranslationHelperを再オープンしてprependでSwitchTranslationHelperを差し込みます。  
これでメソッド呼び出しの順序がSwitchTranslationHelperのtransalteメソッドの方が先に呼ばれるようになります。  
aliasは呼ばれたクロージャ的な動きで呼ばれた段階のメソッドが固定されるようなので再度SwitchTranslationHelperのメソッドを向くように設定しなおしています。  
  
transalateメソッドではキーの先頭に大人ならadult、子供ならchildを挿入します。  
またlazy lookup(先頭に.がついている呼びだし)時に_smart_phoneが含まれるview内の場合ではpcもスマフォも同一の文言を使いたいのでキーから_smart_phoneを削除しています。  
その後superを読んで元のRailsのi18nの流れへ戻しています。  
次にja.ymlを作ります。  
  

```yml
ja:
  # 大人の設定
  adult:
    price:
      show:
        fee: "大人料金"

  # 子供の設定
  child:
    price:
      show:
        fee: "子供料金"
```

これでpriceコントローラのshowアクションのテンプレート内で
```erb
<%= t(".fee") %>
```
とすると、ユーザが大人の場合は大人料金。  
ユーザが子供の場合は子供料金と表示されます。  
  
prefixの条件を変えることで読込先をうまく切り替えることができますね。
