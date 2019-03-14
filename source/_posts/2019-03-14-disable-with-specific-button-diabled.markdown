---
layout: post
title: "railsでsubmitした後にsubmitボタンが押せない現象"
date: 2019-03-14 19:00:25 +0900
comments: true
categories: [ruby,ruby on rails]
---

だいぶ前にdisable_withの挙動デフォルトになったらしいです。
https://github.com/rails/rails/pull/21135/files

検索結果を絞り込んでCSV出力をしたい時等の画面遷移が発生しないsubmitでは非活性になると困りますね。

全体で無効にもできますが()
disable_withを個別に無効にする方法です。

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

submitボタンに'data-disable-with' => falseを設定してあげると無効になります。

```
= f.button :submit, I18n.t(".csv_download"), class: "btn-primary", 'data-disable-with' => false
```

ちなみに全体でfalseにしたい場合はこう

```
class Application < Rails::Application
  config.action_view.automatically_disable_submit_tag = false # この行を追加
end
```

ソース見る限り全体禁止にして、各ボタンで有効にとかはできなさそうなので全体は注意が必要ですね。
