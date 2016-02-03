---
layout: post
title: "Cronがutcで動いてる場合に"
date: 2016-02-03 10:33:11 +0900
comments: true
categories: [cron]
---

Centosでtimezoneの設定が日本時間にしているのにcronが指定時刻に動かず調べました。  
/var/log/cronを見るとログの時間が-9時間。  
JSTで動いてないっぽいです。  
  
以下のコマンドでcronのログの時刻も含めて治ります。  
  
<!-- more -->
  
```
cp /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
service crond restart
service rsyslog restart
```

itamaeのrecipeも修正

```ruby
execute "change time zone" do
  user node[:user]
  command <<-EOF
    sudo cp /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
  EOF
  not_if "date | grep 'JST'"
end

# cronとlogにも反映(再起動)
%w(crond rsyslog).each do |serv|
  service serv do
    action :restart
  end
end
```

