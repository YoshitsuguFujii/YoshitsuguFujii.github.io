---
layout: post
title: "ckeditorのスタイルにカスタムスタイル追加する方法"
date: 2015-06-24 12:41:08 +0900
comments: true
categories: [ruby,ruby on rails,ckeditor]
---
  
Railsに脆弱性があったみたいですね  
[Rails 3.2.22, 4.1.11 and 4.2.2 have been released and more](http://weblog.rubyonrails.org/2015/6/16/Rails-3-2-22-4-1-11-and-4-2-2-have-been-released-and-more/)
  
さっそく今やってるPGのrailsのバージョンをあげました。  
  
  
さて  
WYSIWYGエディタの[ckeditor](http://ckeditor.com/demo)がすごくいい感じで使わせてもらっています。  
  
gemがあったので私はこれを使ってckeditorを利用しています。  
[ckeditor](https://github.com/galetahub/ckeditor)  
  
今回はこのckeditorにプルダウンで表示されるスタイルをカスタマイズする方法です。  
<!-- more -->
  
環境はこんな感じ。  

開発環境  
- rails (4.2.2)  
- simple_form (3.1.0)  
- ckeditor (4.1.1)  
  
インストールがすんでうまく表示されるようになったら、以下の感じに修正します。  
  
  
```js admin/app/assets/javascripts/ckeditor/config.js
config.stylesSet = 'my_styles';
CKEDITOR.stylesSet.add( 'my_styles', [
    // Block Styles
    { name: 'Blue Title',       element: 'h3',      styles: { 'color': 'Blue' } },
    { name: 'Red Title',        element: 'h3',      styles: { 'color': 'Red' } },

    // Inline Styles
    { name: 'Marker: Yellow',   element: 'span',    styles: { 'background-color': 'Yellow' } },
    { name: 'Marker: Green',    element: 'span',    styles: { 'background-color': 'Lime' } },

    // Object Styles
    {
        name: 'Image on Left',
        element: 'img',
        attributes: {
            style: 'padding: 5px; margin-right: 5px',
            border: '2',
            align: 'left'
        }
    }
] );
```
  
するとスタイルの中身が上記のものにきり変わっていると思います。  
  
![ckeditor](/images/blog/ckeditor_custom_style_2015-06-24.png) 

できましたね。

