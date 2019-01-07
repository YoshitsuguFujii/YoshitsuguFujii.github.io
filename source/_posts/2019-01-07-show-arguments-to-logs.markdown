---
layout: post
title: "メソッドの引数の値を全部表示したい時"
date: 2019-01-07 16:44:35 +0900
comments: true
categories: [ruby,ruby on rails]
---

あけましておめでとうございます。  
今年もよろしくお願いします。  
  
さっそくものぐさネタ。  

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

```
  method(__method__).parameters.each do |_, name|
    puts "#{name} => #{binding.local_variable_get(name)}"
  end
```
