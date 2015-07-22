---
layout: post
title: "railsでckeditorにplugin追加"
date: 2015-07-21 10:33:15 +0900
comments: true
categories: [ruby,ruby on rails,ckeditor]
---

ckeditorで作成したinputがsubmitした時にemptyだったらエラーを出したい。  
いろいろ検索しましたが有用な情報がなく、inputにCKEDITOR.stylesSet.addでクラスを設定する方法もうまくいかず、若干途方に暮れましたが、プラグイン[html5validation](http://ckeditor.com/addon/html5validation)をいれることで対応することにしました。  
   
導入手順です。  
  
<!-- more -->
  
[ここ](http://ckeditor.com/addon/html5validation)のページからDownloadを押してダウンロードします。  
落としたzipを解凍して、app/assets/javascripts/ckeditor/pluginsディレクトリを作成して解凍したディレクトリごと放り込みます。  
  
次に日本語化します。  
app/assets/javascripts/ckeditor/plugins/html5validation/langにja.jsを作ります。  
中身はこんな感じですかね。  
  
```js app/assets/javascripts/ckeditor/plugins/html5validation/lang/ja.js
/*
Copyright (c) 2015, Total Web Services. All rights reserved.
*/
CKEDITOR.plugins.setLang('html5validation', 'ja', {
  patternLabel: '検証パターン',
  requiredLabel: '必須?',
  validationTabTitle: '値の検証',
  novalidateLabel: 'HTML5 Validationを無効にする',
  sampleTwitterUsername:  'ツイッターユーザ名のサンプル: ^[A-Za-z0-9_]{1,15}$',
  samplePhonePattern: 'アメリカの電話番号のサンプル: \d{3}[\-]\d{3}[\-]\d{4}',
  sampleZipCode: '<p>アメリカの住所のサンプル: (\d{5}([\-]\d{4})?)</p>',
  html5patternLink: '<p>他のHTML5 Validation patterns を探す <a href="http://html5pattern.com/" title="HTML5 Pattern">html5pattern.com</a></p>'
});
```
  
次に言語を追加します。  
app/assets/javascripts/ckeditor/plugins/html5validation/plugin.jsを開いて8行目のlangにjaを足します。  
```js app/assets/javascripts/ckeditor/plugins/html5validation/plugin.js
/**
 * @license Copyright (c) 2015, Total Web Services. All rights reserved.
 */


CKEDITOR.plugins.add('html5validation', {
  icons: false,
  lang: 'ja,en,fr,es,ru,it', // ここにjaを追加
  init: function(editor) {
    var lang = editor.lang.html5validation;
    editor.filter.allow( {
      textarea: {
        attributes: 'required,pattern',
        propertiesOnly: true
       },
       input: {
        attributes: 'required,pattern',
```
  
次にconfig.jsに追記します。  
  
```js app/assets/javascripts/ckeditor/config.js
CKEDITOR.config.extraPlugins = 'html5validation';
```
  
他にも追加したいpluginがある場合はカンマ区切りで指定するみたいです。  
これで準備は完成です。ckeditorでformを作ろうとするか、既存のformを選択して右クリックしてフォームのプロパティで  
必須が設定できるようになっているかと思います  
  
![完成イメージ](/images/blog/2015-07-21_2.png) 
  
![完成イメージ](/images/blog/2015-07-21.png) 
