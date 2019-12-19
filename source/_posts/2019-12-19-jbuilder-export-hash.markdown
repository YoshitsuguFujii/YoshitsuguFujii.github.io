---
layout: post
title: "jbuilderのtemplateを使ってhashを取得"
date: 2019-12-19 12:26:18 +0900
comments: true
categories: [ruby,ruby on rails, jbuilder]
---

grape-jbuilderの環境でapi/view/api/users/index.jbuilderを使ってjsonを返しているエンドポイントがある。  
これをcontrollerでhashで取得したい場合。  

またapi/view/api/users/index.jbuilderでは@usersを使って値を取り出している。  

config/application.rbに以下の設定を想定

```
class Application < Rails::Application
  config.middleware.use(Rack::Config) do |env|
    env['api.tilt.root'] = Rails.root.join 'app', 'views', 'api'
  end
end
```

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



コントローラーでの取り出し例  

```
@users = User.all
engine = ::Tilt.new(request.env['api.tilt.root'] / 'users/index.jbuilder', nil, view_path: request.env['api.tilt.root'])
hash = JSON.parse(engine.render(self))
```

