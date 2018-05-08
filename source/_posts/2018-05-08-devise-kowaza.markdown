---
layout: post
title: "Deviseちょっとしたtips２つ"
date: 2018-05-08 10:38:56 +0900
comments: true
categories: [ruby,ruby on rails, devise]
---

[Devise](https://github.com/plataformatec/devise)の小技２つです。  

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

１. deviseでログインに失敗した際にアクセスされたページを元に任意のページへ飛ばす。  

```ruby
# config/initializers/devise.rb
Devise.setup do |config|
  config.warden do |manager|
    manager.failure_app = CustomAuthenticationFailure
  end
end
```

```ruby
# lib/custom_authentication_failure.rb
class CustomAuthenticationFailure < Devise::FailureApp
  protected

  def redirect_url
    uri = URI.parse(warden_options[:attempted_path])
    if uri.path.start_with?('/admin')
      hogehoge_url # adminではじまる場合に飛ばしたいurl
    else
      super # それ以外の場合は通常のdeviseの処理へ流す
    end
  end
end
```

warden_options[:attempted_path]でアクセスしようとしたパスが参照できます  


２. ログインエリアのurlにアクセスした時に保存するurlを操作  
  
deviseはデフォルトでログインエリアにアクセスした際に未ログイン状態だとそのアクセスしたパスをsessionに保存してくれ、  
ログイン後にそのurlにリダイレクトしてくれる機能があります。  
  
特定のurlを覚えさせたくなかったのでパッチをあてました。  

``` ruby
# config/initializers/devise.rb
module StoreLocationPatch
  def store_location_for(resource_or_scope, location)
    return if URI.parse(location).path == '/' # rootへのアクセスは記憶させない
    super
  end
end

module Devise
  module Controllers
    module StoreLocation
      prepend StoreLocationPatch
    end
  end
end
```
