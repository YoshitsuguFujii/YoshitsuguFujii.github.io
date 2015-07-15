---
layout: post
title: "ckeditorのテーブルのデフォルト値を変更"
date: 2015-07-15 16:56:33 +0900
comments: true
categories: [ruby,ruby on rails,ckeditor]
---

相変わらず使わせてもらってます。[ckeditor](https://github.com/galetahub/ckeditor)  
デフォルトでテーブルのサイズが500pxとなっておりますが、スマートフォンでも表示する必要がありこれを100%にしたくてしらべました。  
ついでに邪魔なのでborderとcellspacingの値も0にしちゃいましょう。  
  
<!-- more -->
  

```js app/assets/javascripts/ckeditor/config.js
CKEDITOR.on( 'dialogDefinition', function( ev ) {
    var dialogName = ev.data.name;
    var dialogDefinition = ev.data.definition;

    if ( dialogName == 'table' ) {
        var info = dialogDefinition.getContents( 'info' );

        info.get( 'txtWidth' )[ 'default' ] = '100%';
        info.get( 'txtBorder' )[ 'default' ] = '0';
        info.get( 'txtCellSpace' )[ 'default' ] = '0';
    }
});
```
  
テーブルダイアログが初期値で幅100%、枠線の幅0、セル内余白0になっているのが確認できます。  
![完成イメージ](/images/blog/2015-07-15 17.00.59.png) 
