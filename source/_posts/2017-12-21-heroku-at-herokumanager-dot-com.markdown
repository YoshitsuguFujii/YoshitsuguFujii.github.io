---
layout: post
title: "HerokuのNewRelicでRoleがUserで何もできない"
date: 2017-12-21 13:55:13 +0900
comments: true
categories: [Heroku, NewRelic]
---

子供が生まれましたのでしばらく釣りはお休みです。  
春ぐらいになったら行きたいなぁ。  
  
妻が帝王切開で産んだので、12月は週１の無理のない時間での出社を混ぜつつ、フルリモートで働かせていただいております。  
大変ありがたいことです。  
  
育児と家事と仕事で目が回るようでテレビなんてほとんど見れません。  
シングルで育てている人には頭があがりませんね。  
  
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
  
  
今の仕事ではHeroku上でアプリケーション動かしているんですが、AddonでNewRelic足したら  
NewRelic上でAlertの設定や各種変更動作ができない。  
  
Userの設定をみると  
  
チーム名@herokumanager.com  
  
という見慣れないOwner権限を持ったユーザとUserロールの自分のみ。  
色々と調べて見ましたが、最初にAddonを入れた時に連携するのにユーザが必要で暫定的に上記のユーザが作られるっぽい。  
  
https://discuss.newrelic.com/t/authorization-for-heroku-users/52967  
https://discuss.newrelic.com/t/heroku-user-authorization/53121  
  
正しい方法はわかりませんがしばらく調べてもわからなかったので潔くチケットをオープンしてNewRelicのサポートに自分のユーザをAdminにしてくれと夕方お願いしたら夜にはAdminになりました。  
  
何か詰まっている人がいればサポートに投げるといいです。  
誰かの参考になれば。  
