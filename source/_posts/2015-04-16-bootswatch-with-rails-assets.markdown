---
layout: post
title: "Railsでbootswatch導入(with rails assets)"
date: 2015-04-16 11:01:57 +0900
comments: true
categories: [ruby,ruby on rails,bootswatch]
---

管理画面を作るにあたって[bootstrap](http://getbootstrap.com/)だけで1から作るのもしんどかったので、フリーのbootstrap用テンプレート[bootswatch](https://bootswatch.com/)を使って管理画面を作ってみたいと思います。

導入には以下4パターンがあるかと思います。 

1.  [bootswatch-rails](https://github.com/maxim/bootswatch-rails)などのGemを使う
2.  bowerで入れる
3.  rails-assetsで入れる
4.  サイトからダウンロードしてassets下に配置する

今回はrails-assetsで導入してみました。

<!-- more -->

[Rails Assets](https://rails-assets.org/)で検索するといくつか候補が出てきます。  
今回はrails-assets-bootswatch-sassを使ってみます

```ruby Gemfile
source 'https://rails-assets.org' do
  gem 'rails-assets-bootstrap-sass-official'
  gem 'rails-assets-bootswatch-sass'
end
```


そしてapplication.cssをapplication.css.scssにリネームして以下を追加です。

```css app/assets/stylesheets/application.css.scss
$icon-font-path: "bootstrap-sass-official/";

@import "bootstrap-sass-official";
@import "bootswatch-sass/flatly/variables";
@import "bootswatch-sass/flatly/bootswatch";
@import "bootswatch-sass/flatly/bootstrap";
```

これでサイトがbootswatchのテーマが適用された状態(今回はフラットデザインのflatlyを選びました)となります。
簡単ですね。

