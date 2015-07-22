---
layout: post
title: "Bootstrap FileInputで日本語の文字が表示されない"
date: 2015-07-17 13:46:05 +0900
comments: true
categories: [ruby,ruby on rails]
---

今の案件で[Bootstrap FileInput](http://plugins.krajee.com/file-input)を使っていますが、日本語(2byte文字)が含まれるファイルを選択した場合ファイル名から日本語部分が消えるという現象が発生しました。  
  
https://github.com/kartik-v/bootstrap-fileinput/blob/master/js/fileinput.js#L496  
ここを見る限りslugCallbackというメソッドを宣言時のoptionに渡してあげるといけそうです。  
  
<!-- more -->
  

すいません。coffeescriptです。
```js
$('.upload_file').fileinput
  showPreview: false
  maxFileCount: 1
  browseClass: 'btn btn-info fileinput-browse-button'
  browseIcon: ''
  browseLabel: ' ファイル選択'
  showRemove: false
  showUpload: false
  slugCallback:
    (text)->
      text.split(/(\\|\/)/g).pop()
```

これで日本語も表示できるようになりました。  
![完成イメージ](/images/blog/2015-07-17 13.52.17.png) 
