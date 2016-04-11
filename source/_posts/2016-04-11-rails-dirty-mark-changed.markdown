---
layout: post
title: "model.changesで強制的に変更したことにする方法"
date: 2016-04-11 18:26:43 +0900
comments: true
categories: [ruby,ruby on rails]
---

RailsのActiveModelのDirtyでは変更前、変更後、変更したかなどモデルクラスの新旧状態が取れる。  
http://qiita.com/kakkunpakkun/items/b286f789ca8590104ad1  
  
このdirtyを使って変更してないけど変更したことにしたい(observerを再実行したい時とか)に使える技  
  
```ruby
user = User.first
user.changes # => {}
user.created_at_will_change! # => => 2015-01-16 20:09:59 +0900
user.changes 
# => {
#  "created_at" => [
#    [0] 2015-01-16 20:09:59 +0900,
#    [1] 2015-01-16 20:09:59 +0900
#   ]
# }
```
  
属性名_will_change!で新旧に同じ値が設定される。  
 
なんでこんなことやりたかったかっていうとobserverでuserのnameの値が変更されたらapiを叩く処理を書いていたのだけど、急遽全件apiを再実行する必要があり強制的にnameが変更されたことにしたかったため調べたらあったというお話
