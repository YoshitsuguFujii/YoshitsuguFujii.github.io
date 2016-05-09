---
layout: post
title: "csvなどで多重配列を縦横を入れ替える(行列切り替え)"
date: 2016-05-09 10:51:15 +0900
comments: true
categories: [ruby]
---
  
小ネタです。  
Csvファイルなどで行列を切り替えて出力したい場合はtransposeが便利です。  
  
<!-- more -->  
  
```ruby  
csv.each{|row| p row.join(",") }  
csv = [["user_name", "age", "gender"], ["藤井", "36", "men"]]  
csv.each{|row| p row.join(",") }  
# => "user_name,age,gender"  
# => "藤井,36,men"  
  
csv.transpose.each{|row| p row.join(",") }  
# => "user_name,藤井"  
# => "age,36"  
# => "gender,men"  
```  
