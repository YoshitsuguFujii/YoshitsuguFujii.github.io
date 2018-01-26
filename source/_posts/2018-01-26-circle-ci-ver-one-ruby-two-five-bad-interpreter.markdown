---
layout: post
title: "CircleCIでRuby2.5使おうとしたらbad interpreter"
date: 2018-01-26 18:51:07 +0900
comments: true
categories: [ruby,ruby on rails, circlci]
---

一度は成功していたのに急にCircleCI 1.0がこけるようになってしまいました。  
  
```  
set ruby version to 2.5.0  
```  
  
で  
  
```  
ruby-2.5.0 - #generating default wrappers|/-\|/-\|.-\|/-\|/-.|/-\|/-\|.-\|/-\|/-.|/-\|/-\|.-\|/-\|/-.|/-\|/-\|.-\|/-\|.  
Using /home/ubuntu/.rvm/gems/ruby-2.5.0  
/home/ubuntu/.rvm/scripts/override_gem: /home/ubuntu/.rvm/rubies/ruby-2.5.0/bin/gem: /home/travis/.rvm/rubies/ruby-2.5.0/bin/ruby: bad interpreter: No such file or directory  
```  
  
のようなエラー。  
  
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
  
  
調べるとこんなツイートが...  
  
<blockquote class="twitter-tweet" data-lang="ja"><p lang="en" dir="ltr"><a href="https://twitter.com/circleci?ref_src=twsrc%5Etfw">@circleci</a> I&#39;m getting build failures on ruby 2.5 - it looks like the `gem` command is using a shebang that references a non-existent `/home/travis` directory <a href="https://t.co/UcZREdK2Q8">pic.twitter.com/UcZREdK2Q8</a></p>&mdash; Justin Rhinesmith (@jerhinesmith) <a href="https://twitter.com/jerhinesmith/status/956311677426741248?ref_src=twsrc%5Etfw">2018年1月24日</a></blockquote>  
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>  
  
sshするとshebangでエラーになっている同じ現象でした。  
そしてこんなチケットが...  
  
[Ruby 2.5.0 builds failing on CircleCI 1.0 due to missing `/home/travis`](https://discuss.circleci.com/t/ruby-2-5-0-builds-failing-on-circleci-1-0-due-to-missing-home-travis/19620)  
  
解決しているらしいのですが、普通にエラーでますがな...  
マニュアルで  
  
```  
machine:  
  pre:  
    - sudo ln -s /opt/circleci /home/travis  
```  
  
を仕込もうとしたらもうありますエラー。  
そして微妙にパスも違う。  
  
結論。こうしてなおしました。  
  
```  
machine:  
  pre:  
    - sudo rm -rf /home/travis  
    - sudo ln -s /home/ubuntu /home/travis  
  ruby:  
    version: 2.5.0  
```  
  
すぐなおってくれると思いますが、お困りの人がいたら上記で治ります。  


