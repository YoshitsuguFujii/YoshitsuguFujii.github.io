---
layout: post
title: "Railsでbootstrap datetimepickerを日本語化しつつ導入(with rails assets)"
date: 2015-04-22 21:52:21 +0900
comments: true
categories: [ruby,ruby on rails,bootswatch]
---


普段はよく[bootstrap-datepicker](https://github.com/eternicode/bootstrap-datepicker)を使うのですが、
今回の案件では時刻まで絞り込みたいというお話だったのでdatetimepickerを導入してみました。  


こちらを使います。かなり高機能な上に日本語化まで対応できる優れもの。  
[Eonasdan/bootstrap-datetimepicker](http://eonasdan.github.io/bootstrap-datetimepicker/)  
  
デモを見るだけでもワクワクしますね。  
[入れ方](http://eonasdan.github.io/bootstrap-datetimepicker/Installing/)もたくさん用意されていていいですね。  
今回もrails-assetsで導入します。  
  
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
  
```ruby Gemfile
source 'https://rails-assets.org' do
  gem 'rails-assets-bootstrap-sass-official'
  gem 'rails-assets-eonasdan-bootstrap-datetimepicker'
end
```
  
`bundle install`したら次はapp/assets/javascripts/application.jsに以下を記述。  
日本語化する場合はmomentも一緒にいれます。  
  
```js app/assets/javascripts/application.js
//= require bootstrap-sass-official
//= require moment
//= require moment/locale/ja
//= require eonasdan-bootstrap-datetimepicker
```
  
最後にcssの読み込み  
```css app/assets/stylesheets/application.css.scss
@import "bootstrap-sass-official";
@import "eonasdan-bootstrap-datetimepicker";
```
  
これで設定は終了です。  
あとはhtmlで  
  
```html
<input class="datetimepicker form-control" type="text">
```
  
とかやって  
  
```js
  $(".datetimepicker").datetimepicker({
    format: "YYYY/MM/DD HH:mm",
    showClear: true,
    showClose: true,
    locale: 'ja'
   });
```
  
とかやると日本語かされた時刻も選択可能なカレンダーがいい感じに表示されるはずです。
  
### 2014/04/25追記  
  
ちなみに書式設定をいじれば時刻選択なしの日付のみのdatepickerとしても使えます。  
  
```js
  $(".datepicker").datetimepicker({
    format: "YYYY/MM/DD",
    showClear: true,
    showClose: true,
    locale: 'ja'
   });
```
  
非常にいいですね。
