---
layout: post
title: "FaradayとHttpartyでクッキーを付与したい場合"
date: 2015-10-16 17:33:25 +0900
comments: true
categories: [ruby,ruby on rails,faraday, httparty]
---

同一ドメインなんだけどホスト名が違うシステムがあり、WEBサーバーからApiサーバーへjsonを取得するべくgetリクエストを投げる。  
その際にWEBサーバーでログイン時に発行されたクッキーをリクエストに付与してapiサーバーではそのクッキーからユーザを特定するような処理が必要になった。   
  
FaradayとHttpartyでクッキーを付与する方法を調べた。
  
<!-- more -->
  
## Faraday  
[miyagawa/faraday-cookie_jar](https://github.com/miyagawa/faraday-cookie_jar)というクッキーをよしなに扱ってくれるみたいですが、特に使わなくてもいけました。

```ruby
client = Faraday.new 'http://example.com/api/list' do |b|
  b.request :json
  b.request :url_encoded
  b.adapter Faraday.default_adapter
end

res = client.get '' do |req|
  req.headers['Cookie'] = request.headers["HTTP_COOKIE"]
end

puts res.body
```

## Httparty

```ruby
include HTTParty

self.default_cookies.add_cookies(cookies.to_h)
res = self.get('http://example.com/api/list')
p res.inspect
```
  
お手軽ですね!

### 2015年10月19日追記

Httpartyのヘッダに乗せるタイプ  
  
```ruby
include HTTParty

res = self.get('http://example.com/api/list', headers: "Cookie" => request.headers["HTTP_COOKIE"])
p res.inspect
```
