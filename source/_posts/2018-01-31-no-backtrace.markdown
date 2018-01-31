---
layout: post
title: "ruby2.5にあげたら例外発生時にstacktraceが吐き出されなくなった"
date: 2018-01-31 11:51:51 +0900
comments: true
categories: [ruby,ruby on rails]
---

Railsである日を境に例外発生時にスタックトレースがでなくなっちゃいました。  

```
F, [2018-01-31T11:41:40.388898 #5662] FATAL -- : 
NoMethodError - undefined method `company_users' for nil:NilClass:
```

railsのlogにはこれしかでない。  

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


検索すると発見。  
  
[Why does Ruby 2.5.0 not show a stacktrace in Rails 5.1.4?](https://stackoverflow.com/questions/48327987/why-does-ruby-2-5-0-not-show-a-stacktrace-in-rails-5-1-4)
  
なるほどrubyを2.5にあげたからか。  
  
手元の環境を見ると  

```
ruby (2.5)  
rails (5.1.4)  
binding_of_caller (0.7.2)
```

0.8.0にbinding_of_callerにあげて解決です。  
