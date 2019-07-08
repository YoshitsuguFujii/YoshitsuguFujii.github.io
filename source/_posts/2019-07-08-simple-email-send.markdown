---
layout: post
title: "Railsでお手軽にメールを送る"
date: 2019-07-08 11:15:30 +0900
comments: true
categories: [ruby,ruby on rails]
---

お手軽にある特定のエラーなどの情報を簡単なワンメッセージを関係者に送りたい場合、  

```
rails generate mailer UserMailer
```

とかやってテンプレートつかってってやると重厚すぎてつらい。

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


ワンライナーでメールを送るにはこうやればいい。  

```
ActionMailer::Base.mail(from: 'error@example.com', to: 'admin@example.com', subject: '何かエラーが発生しました', body: '設定を見直してください').deliver
```

現場からは以上です。  
