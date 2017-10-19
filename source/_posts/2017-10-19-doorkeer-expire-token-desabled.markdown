---
layout: post
title: "Doorkeeperでトークンの有効期限切れをさせない"
date: 2017-10-19 11:59:10 +0900
comments: true
categories: [ruby, ruby on rails]
---

釣りばっかりの記事の連投にぶっこんですいません。  
IT技術ネタです。  
  
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
    
  
こちらを読みまして。  
[アクセストークンに有効期限を設けるべきかどうか](https://qiita.com/r7kamura/items/3e03471e02ea9ab5902a)  
  
たしかにそうだよなと思ったのとDoorkeeper使った認証基盤にクライアントがアクセスするときに、token失効により、他ユーザの情報などがとれないことがったりしたのでtoken失効させないことにします。  
  
``` ruby  
# config/initializers/doorkeeper.rb  
  
Doorkeeper.configure do  
  access_token_expires_in nil # <- こうする  
end  
```  
  
参考  
[Customizing Token Expirationupdate oauth_access_tokens set expires_in = null](https://github.com/doorkeeper-gem/doorkeeper/wiki/Customizing-Token-Expiration#access-token)  
  
もうすでに設定されているアプリでこちら無効にしたい場合は  
expires_inにnullを設定すればいいようです。  
  
```sql  
update oauth_access_tokens set expires_in = null  
```  
