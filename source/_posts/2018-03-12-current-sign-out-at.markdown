---
layout: post
title: "最後にサインアウトした時間が欲しい"
date: 2018-03-12 12:51:13 +0900
comments: true
categories: [ruby,ruby on rails, devise]
---


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


before_logoutというwardenのフックメソッドがあるのでそこに追加  
自分はconfig/initializers/devise.rbに追加しちゃいました。  

```ruby
Warden::Manager.before_logout do |user,auth,opts|
  if user.is_a?(User)
    user.update_column(:current_sign_out_at, Time.current)
  end
end
```

