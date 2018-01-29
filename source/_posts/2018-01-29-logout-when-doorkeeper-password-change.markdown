---
layout: post
title: "Doorkeeperでパスワードが変更されたらクライアント側でログアウトさせる"
date: 2018-01-29 14:26:19 +0900
comments: true
categories: [ruby,ruby on rails, doorkeeper]
---

[Doorkeeper](https://github.com/doorkeeper-gem/doorkeeper)を使ったoauthの仕組みでプラットフォーム側でパスワード変更の処理を書いててパスワード変更しても現在ログイン状態のユーザーがログアウトされなくて久しぶりにどっぷりハマったのでメモ。  
  
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
  
セッションストアであるredisを見ると同じユーザーで二つのブラウザでログインすると２つ違うsession keyで保持されている。  
このセッションの中身は  
  
一つ目はwarden key(そのままだとユーザID)  
二つ目はencrypted_password  
  
となっているようだ。  
  
https://stackoverflow.com/a/23683925/2223472  
  
Doorkeeperを使っているクライアントではoauth認証を使うのでパスワードを使っていない  
そのため二つめencrypted_passwordの値は空になっている。  
  
この二つの値を使って認証しているようだ。
  
プラットフォーム側でパスワードを変更しても二つ目の値は相変わらず空のためクライアント側ではセッションの状況に変化なしとなり  
そのままログインが保持されてしまう。  
  
tokenを使ったりパスワードを渡す等があったりしたがこうした。  
  
まず、 パスワード変更時刻をプラットフォーム側にcolumnとして追加。  
パスワード変更時にupdateするようにする。  
  
```  
class User < ApplicationRecord  
  def reset_password(*args)  
    self.password_changed_at = Time.current  
    super  
  end  
end  
```  
  
そしてクライアントサイドでは連携されたpassword_changed_atを使うようにする。  
  
```  
class User < ApplicationRecord  
  def authenticatable_salt  
    password_changed_at  
  end  
end  
```  
  
これでパスワードが変更されるとクライアントのセッションの中身も変わり、  
他のブラウザでログインしていても適切にログアウトされる。  
