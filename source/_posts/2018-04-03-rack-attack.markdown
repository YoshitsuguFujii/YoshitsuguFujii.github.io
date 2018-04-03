---
layout: post
title: "Heroku小ネタ"
date: 2018-04-03 11:43:22 +0900
comments: true
categories: [ruby,ruby on rails, heroku]
---

HerokuでRailsアプリ動かすときは  
[rack-attack](https://github.com/kickstarter/rack-attack)  
入れといたほうが良さそう。  

https://help.heroku.com/HCCDCDYY/does-heroku-offer-ddos-denial-of-service-mitigation

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

ちなみにthrottleの機能を使ってみたんですが、cacheを使って試行回数を判定するのでcache storeをnull_storeとかにしちゃうといつまでもローカルではテストできません。  
  
最近釣りに行き始めてますが連続坊主中。  
