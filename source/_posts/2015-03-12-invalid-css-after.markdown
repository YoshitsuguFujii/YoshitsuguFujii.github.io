---
layout: post
title: "開発環境(development)で問題なかったのにdeployしようとしたらprecompileでこける。"
date: 2015-03-12 23:58:19 +0900
comments: true
categories: [ruby on rails ruby]
---

```
rake aborted!
Sass::SyntaxError: Invalid CSS after "...idth: Infinity%": expected expression (e.g. 1px, bold), was "; }"
  (in /var/www/hogehoge/app/assets/stylesheets/fuga/fuga.css.scss)
(sass):12
```

見た目にも問題なさそうだし、念のため[scss-lint](https://github.com/causes/scss-lint)とかも通してみたけど問題なさそう。

<!-- more --> 

原因究明のためちょっとずつソースを削ってはコンパイルを繰り返したところ、
compile errorになっていた原因はここにありました。
わかりますか？

```scss
$colNums: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10;
@each $colNum in $colNums {
  &.col-#{$colNum}{
    width: 100% / $colNum;
  }
}
```



エラー文言がsyntax error的なので構文エラーな箇所を必死に探していました。
結局0で割ってるからというどうしようもないミスでした。
結果こうなりました。 

```scss
$colNums: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10;
@each $colNum in $colNums {
  &.col-#{$colNum}{
    @if $colNum == 0{
      width: 100%;
    }@else{
      width: (100% / $colNum);
    }
  }
}
```
