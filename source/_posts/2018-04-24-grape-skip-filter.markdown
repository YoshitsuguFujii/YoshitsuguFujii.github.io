---
layout: post
title: "Grapeでskip_before_action"
date: 2018-04-24 14:10:09 +0900
comments: true
categories: [ruby,ruby on rails, grape]
---

Deviseとか使っていて、ApplicationControllerに認証の仕組みを設ける時。  
  
```ruby
class ApplicationController < ActionController::Base
  before_action :authenticate_user!
end
```
  
こう書きますね。  
ログイン前のページではログイン判定は不要なのでskip_before_actionでskipさせるのが定石だと思います  
  
```ruby
class HogeController <  ActionController::Base
  skip_before_action :authenticate_user!
end
```
  
Grapeではどうやんのよっていう話し。  
  
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


route.settingsを使うといいです  

```ruby
module API::V1
  class Root < Grape::API
    before do
      user_authenticate! unless route.settings[:skip_user_authenticate]
    end

    mount Users
  end
end
```

```ruby
module API::V1
  class Users < Grape::API
    resource 'users' do
      route_setting :skip_user_authenticate, true
      get do
        # do something
      end
    end
  end
end
```
