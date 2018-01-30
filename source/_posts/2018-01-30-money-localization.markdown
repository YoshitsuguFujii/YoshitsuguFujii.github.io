---
layout: post
title: "国際化対応(お金関連)"
date: 2018-01-30 10:27:12 +0900
comments: true
categories: [ruby,ruby on rails]
---

国際化対応。  
DBの日時はutcで保存して表示する時にユーザによって該当のタイムゾーンで表示とか色々な知見はあるかと思いますが。  
お金の区切りもあるんですね。  
  
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
  
お金ってカンマ区切りだけかと思い込んでましたがそうじゃないらしいです。  
  
[世界各国での数字の区切り方](http://coliss.com/articles/build-websites/operation/writing/53.html)  
  
数値がペリオドで送られてくる国やスペースで送ってくる国があるので注意が必要です。  
カンマ一括で消して処理していたらバリデーションで特定の国入力エラーになっていた。  
  
jsのtoLocaleStringとか使うとドイツのブラウザではカンマではなくペリオド入るらしいので気をつけるべし
