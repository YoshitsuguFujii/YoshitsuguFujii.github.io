---
layout: post
title: "Railsが自動で出すログ"
date: 2019-06-25 17:58:29 +0900
comments: true
categories: [ruby,ruby on rails]
---

StackDriverのgemを入れたらRails.loggerでlog/以下に出力できなくなってしまって調べたのでメモ。  

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

StackDriverを利用するとCloudLogging用のLoggerにRails.loggerが差し変わります。  

```ruby
Rails.logger.info('hogehoge')
```

とやってもRAILS_ROOT/log/production.logには表示されません。  
  
だけどRailsが自動で出力してくれるController名やAction名。レンダリングしたviewの情報はlog/production.logに表示されます。  
  
なんでかなと思って調べてみたらこちらControllerはRails.loggerに設定されているloggerを使ってませんでした。  
  
https://github.com/rails/rails/blob/master/actionpack/lib/action_controller/log_subscriber.rb#L76  
  
結局サーバー側でGoogle fluendが動かしてそっちで吸い上げることができるので  

```ruby
Rails.application.configure do
  config.google_cloud.use_logging = false
end
```

にしました。  
お手軽にlog/production.logで確認できて良い感じです。  
