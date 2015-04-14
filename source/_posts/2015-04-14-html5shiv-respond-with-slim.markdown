---
layout: post
title: "RailsでIE8の対応(with rails assets)"
date: 2015-04-14 11:30:58 +0900
comments: true
categories: [ruby ruby on rails]
---

IE8も推奨ブラウザに含めるプロジェクトに関わることになったのでrails-assetsを使ってIE8に対応していきたいと思います。

やることとしては以下の感じですかね。

- jquery1.x系の導入(今回は1.9.1)
- html5shiv.jsの導入
- respond.jsの導入 

テンプレートエンジンはslimを使います。

<!-- more -->

まずはGemfile

```ruby Gemfile
# Slim
gem "slim-rails"

source 'https://rails-assets.org' do
  gem 'rails-assets-jquery', "1.9.1"

  # for ie8
  gem 'rails-assets-html5shiv'
  gem 'rails-assets-respond'
end
```

`bundle install`したらconfig/initializers/assets.rbに以下を追記

```ruby config/initializers/assets.rb
Rails.application.config.assets.precompile += %w(html5shiv.js respond.js)
```

そしてapp/views/layouts/application.html.slimで読み込みます。
```haml app/views/layouts/application.html.slim
/[if lt IE 9]
  = javascript_include_tag "html5shiv"
  = javascript_include_tag "respond"
```

最後に`app/assets/javascripts/application.js`でjqueryを読み込みます

```js app/assets/javascripts/application.js
//= require jquery
```

以上でhtml5shiv.jsとrespond.js、そしてIE8に対応しているjquery1.x版の導入がrails-assetsにて完了です。
