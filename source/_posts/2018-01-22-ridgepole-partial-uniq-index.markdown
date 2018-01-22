---
layout: post
title: "ridgepoleの部分インデックスで毎回createとdrop indexをさせない書き方"
date: 2018-01-22 14:44:35 +0900
comments: true
categories: [ruby,ruby on rails, ridgepole]
---

railsでparanoiaとか使っていて、  
mysqlで論理削除にdeleted_atがnullかどうかで論理削除判定していると  
dbのuniq indexで辛い感じになります。  
  
postgresqlでは部分インデックスなるものがあり、deleted_atがnullのものをuniq制約の条件に含めないとかいうことができます。  
  
[部分インデックス](https://www.postgresql.jp/document/9.2/html/indexes-partial.html)  
  
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
  
ridgepoleではこう書くと毎回create index drop indexしません。  
  
  
```  
add_index "users", ["email"], name: "uniq_email_idx", unique: true, where: '(deleted_at IS NULL)'  
```  
  
本日は大雪らしいので早く帰りましょう。  

