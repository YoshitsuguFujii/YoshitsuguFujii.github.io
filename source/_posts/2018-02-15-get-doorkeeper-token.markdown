---
layout: post
title: "Doorkeeperの認証で使われるtokenを取得"
date: 2018-02-15 11:46:00 +0900
comments: true
categories: [ruby,ruby on rails, doorkeeper]
---
  
doorkeeprの処理のなかで削除済のユーザのステータスが取りたくて、tokenを取得できるかやってみました。  
  
イレギュラーアクセス -> tokenが取れない。  
通常のアクセス -> tokenが取れる。ユーザがひける。  
削除済または退会済みユーザへのアクセス -> tokenが取れる。ユーザがひけない。  
  
って感じでしょうか？  

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
  
下記のようにしたら取れました。  
あとはこれをうまくつかってerrorとかで削除済のレスポンスを返す感じですね。  
  
```ruby
# frozen_string_literal: true

require 'doorkeeper/grape/helpers'

module V1
  class Root < Grape::API
    helpers Doorkeeper::Grape::Helpers

    before do
      authorization_request = Doorkeeper::Grape::AuthorizationDecorator.new(request)

      # これでrequestからtokenが取れる
      token = Doorkeeper::OAuth::Token.from_request(authorization_request, *Doorkeeper.configuration.access_token_methods)

      # そのトークンを使ってユーザのトークンが取れる。
      access_token = Doorkeeper::AccessToken.by_token(token)

      doorkeeper_authorize!
    end

    mount Users
  end
end
```
