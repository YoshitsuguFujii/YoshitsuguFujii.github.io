---
layout: post
title: "特定のユーザに送られるメールを全てキャンセル"
date: 2017-10-26 11:36:00 +0900
comments: true
categories: [ruby, ruby on rails]
---
  
ナチュラムでジョイナス2000が2000円切っていたのでぽちり。  
昨日届きましたがやっぱりちょっと安い感じがありました。  
もう千円だしてアリビオ買えばよかったかも。  
  
釣り記事の合間のITネタ。  
  
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
  
usersテーブルにlockフラグを持っているなどある条件によってメールを送信するしないをアプリ全体で制御する方法。  
interceptorを使います。  
  
``` ruby    
# config/initializers/mail_interceptor.rb  
  
ActionMailer::Base.register_interceptor(MailInterceptor)  
```  
  
``` ruby    
# lib/mail_interceptor.rb  
  
class MailInterceptor  
  def self.delivering_email(email)  
    # lock中のユーザーには送らない  
    user = User.find_by(email: email.to)  
    if user.lock_flag?  
      email.perform_deliveries = false  
    end  
  end  
end  
```  
  
perform_deliveriesにfalseを設定するとメールが送られなくなります。  
