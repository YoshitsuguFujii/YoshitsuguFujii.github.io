---
layout: post
title: "[小ネタ]Railsを使っていてAngularのform validationだけしたい時"
date: 2016-03-23 12:36:53 +0900
comments: true
categories: [ruby,ruby on rails, angular]
---

railsでシステムを作っていて、ここはゴリゴリ書くようなところじゃないけどformのvalidationだけangularの機能使いたくなったんですが。
railsはname属性に[]を使うので、この場合のng-show内部の指定がわからなかったので備忘録も兼ねて。  
  
```erb
form name="new_form"
  input name="price[hoge]" ng-model="price" ng-pattern="/^([1-9]\\d*|0)(\\.\\d+)?$/" required="" type="text" /
  span.text-error ng-show="newForm['price[hoge]'].$error.required"  ※
  span.text-error ng-show="newForm['price[hoge]'].$error.pattern"  This is not a valid dollar.
```
  
formのnameに指定されいるものを[]とシングルクォートで囲むといけます。 
