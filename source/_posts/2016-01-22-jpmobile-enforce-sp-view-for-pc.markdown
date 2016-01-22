---
layout: post
title: "jpmobileでPCでスマートフォンのビューを表示する場合"
date: 2016-01-22 18:51:27 +0900
comments: true
categories: [ruby,jpmobile]
---

なんらかの条件で強制的にスマートフォンのviewを使いたい時。
アプリのwebviewとか？

```ruby
before_filter :hoge
def hoge
  if true # 条件
    env["rack.jpmobile"] = Jpmobile::Mobile::SmartPhone.new(env, request)
    register_mobile
  end
end
```
