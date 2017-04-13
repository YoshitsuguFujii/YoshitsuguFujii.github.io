---
layout: post
title: "postgres使っていてridgepoleで文字列から数値型への変換エラー"
date: 2017-04-13 11:19:54 +0900
comments: true
categories: [ruby, gem, ridgepole]
---


表題のことをやろうとしたらエラーがでた。  
  
```
[ERROR] PG::DatatypeMismatch: ERROR:  column "count" cannot be cast automatically to type integer
HINT:  You might need to specify "USING count::integer".
```
  
Schemafileをこうすればいけました。 
  
```
create_table "products", force: :cascade do |t|
  t.integer  'count',             using: 'count::integer'
end
```
