---
layout: post
title: "GrapeでCSRF対策"
date: 2018-02-26 17:38:51 +0900
comments: true
categories: [ruby,ruby on rails, grape]
---

セキュリティ試験を受けたらapiでpostしている箇所がcsrf違反でレポートが上がってきました。  
apiでcsrfって必要なの？  
って感じでしたが実際にレポートあがってきてしまっているしよくよく考えたら必要じゃんってなったので実装します。  
  
ググってみると情報が少ないですね。  
みなさんgrapeでcsrfどうしているんでしょう。  
  
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

gistでサンプル見つけましたが、  
[jandudulski/auth.rb](https://gist.github.com/jandudulski/f3799ca67b7b08ded0c6)  
Rails5.1.4では動きません。  
  
[actionpackのcsrfの実装](https://github.com/rails/rails/blob/master/actionpack/lib/action_controller/metal/request_forgery_protection.rb)  
をみながら実装してみます。  
  
結果としてはこんな感じになりました。  
  

```ruby
module V1
  module RequestForgeryProtectionHelper
    def session
      env['rack.session']
    end

    def protect_against_forgery
      unless verified_request?
        Rails.logger.error("[API] Can't verify CSRF token authenticity.")
        error!(
          {
            result: 401,
            status: 'error',
            type: 'csrf_error',
            message: "Can't verify CSRF token authenticity."
          }, 401
        )
      end
    end

    def verified_request?
      !protect_against_forgery? || request.get? || request.head? ||
        authenticity_token_valid?
    end

    def authenticity_token_valid?
      encoded_masked_token = request.headers['X-CSRF-Token'] || request.headers['X-Csrf-Token']
      return false if encoded_masked_token.blank?
      masked_token = Base64.strict_decode64(encoded_masked_token)

      anonymous_controller = ApplicationController.new
      if masked_token.length == ActionController::RequestForgeryProtection::AUTHENTICITY_TOKEN_LENGTH
        anonymous_controller.send(:compare_with_real_token, masked_token, session)
      elsif masked_token.length == ActionController::RequestForgeryProtection::AUTHENTICITY_TOKEN_LENGTH * 2
        csrf_token = anonymous_controller.send(:unmask_token, masked_token)

        anonymous_controller.send(:compare_with_real_token, csrf_token, session)
      else
        false # Token is malformed.
      end
    end

    def protect_against_forgery?
      allow_forgery_protection = Rails.configuration.action_controller.allow_forgery_protection
      allow_forgery_protection.nil? || allow_forgery_protection
    end
  end
end
```

```ruby
module V1
  class Root < Grape::API
    helpers RequestForgeryProtectionHelper

    before do
      protect_against_forgery
    end
  end
end
```

クライアント側  

```javascript
var token = $( 'meta[name="csrf-token"]' ).attr( 'content' );
$.ajaxSetup({
  beforeSend: function ( xhr ) {
    xhr.setRequestHeader( 'X-CSRF-Token', token );
  }
});
```
