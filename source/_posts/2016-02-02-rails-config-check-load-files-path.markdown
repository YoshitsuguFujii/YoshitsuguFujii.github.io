---
layout: post
title: "RailsConfig(現Config)で読み込んでいるファイルのパスを得る"
date: 2016-02-02 11:13:32 +0900
comments: true
categories: [ruby,rails config,config]
---

内部的にrails_envを書き換えたりしていて、rails_configの値がただしくRAILS_ENVの設定通りにファイルを読み込んでくれない事象が発生したので。  
  
rails_config (0.4.2)  
  
```ruby
irb(main):014:0> Settings.instance_variable_get(:@config_sources)
=> [#<RailsConfig::Sources::YAMLSource:0x0055a28d40ab48 @path="Rails_rootへのパス/current/config/settings.yml">, #<RailsConfig::Sources::YAMLSource:0x0055a28d40aa30 @path="Rails_rootへのパス/current/config/settings/hoge.yml">, #<RailsConfig::Sources::YAMLSource:0x0055a28d40a9e0 @path="Rails_rootへのパス/current/config/environments/hoge.yml">]
```

