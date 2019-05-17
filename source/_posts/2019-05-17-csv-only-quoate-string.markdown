---
layout: post
title: "CSVで文字列だけダブルクォートで囲みたい場合"
date: 2019-05-17 18:11:31 +0900
comments: true
categories: [ruby,ruby on rails]
---

数値は囲わず、文字列はダブルクォートで囲みたい。  
`force_quotes: true`だと数値もクォートされる。  

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

### 結論  
`quote_char: ''`をオプションに指定して、渡す文字列の前後をダブルクォートで囲む。  
  
  
### 解説  
  
ここで`force_quotes:true`の分岐  
  
https://github.com/ruby/csv/blob/master/lib/csv/writer.rb#L139  
  
ここで`force_quotes:false`だった場合の分岐  
  
https://github.com/ruby/csv/blob/master/lib/csv/writer.rb#L147  
  
""hoge""  
  
のような文字列を作っていると@quotable_patternに一致してしまい、  
  
"""hoge"""  
  
のような文字になってしまう。  
  
のでquate_fieldで囲まれても問題ないようにから文字を設定してあげる。  
https://github.com/ruby/csv/blob/master/lib/csv/writer.rb#L129-L136  
