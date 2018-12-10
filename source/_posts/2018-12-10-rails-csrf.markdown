---
layout: post
title: "たまにCan't verify CSRF token authenticity."
date: 2018-12-10 13:17:41 +0900
comments: true
categories: [ruby,ruby on rails]
---

時々ログにCan't verify CSRF token authenticity.が表示されます。  
なんでかなーと思っていたら一つ再現パターンを見つけました。  

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

1. Aフォームを表示  
2. Bフォームを別タブで表示  
3. Bフォームでsubmit  
4. AフォームでsubmitでCan't verify CSRF token authenticity.発生。  
  
AフォームとBフォームで同じcsrf tokenが発行されますが、Bフォームのsubmitでcsrf tokenが使われて値が変わってしまうため  
Aフォームのcsrf tokenが古くなってしまいcsrfエラーが発生します。  
  
ごく稀によくあるやつでした。  
