---
layout: post
title: "Railsでtextとhtmlのviewを用意するもmultipart/alternativeにならない問題"
date: 2017-09-12 18:36:54 +0900
comments: true
categories: [ruby, ruby on rails]
---

Ruby: 2.4.1  
Rails: 4.2.8  
  
ActionMailerでhogehoge.html.hamlとhogehoge.text.hamlを用意しているのにtext/htmlの形式でしか送信されなくてはまりかけた。  
hogehoge.text.hamlをhogehoge.text.erbに直したらmultipart/alternativeになってくれた。  
  
textの方はerbじゃないとダメっぽい。  
  
そしてletter_openerってalternativeなメールの時って右上にHTMLメールとプレーンテキストメールの切り替えリンクが付いているんですね。  
めっちゃ便利！  
