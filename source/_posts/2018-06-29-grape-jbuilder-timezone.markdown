---
layout: post
title: "grape-jbuilderでtimezoneをなんとかする"
date: 2018-06-29 15:47:34 +0900
comments: true
categories: [ruby,ruby on rails, grape, jbuilder]
---

Grapeでtimezoneを考慮して日付を返す場合は  
この[What is the best way to emulate `around_filter` when using Grape?](https://t.co/Fqt42F1M3H)のようにgrapeのbeforeとafterを使って設定するのが定石な気がしますが。  
[grape-jbuilder](https://github.com/milkcocoa/grape-jbuilder)でjbuilderの中で書いた日付がbeforeで設定したtimezoneで表示してくれなかったので対応しました。  

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

grapeのendpoint(controllerでいうactionみたいな)で返すとtimezoneが考慮されていますが、jbuilderの中では考慮されていないっぽい。  
before -> grapeのendpointの処理 -> after -> jbuilderのrender  
という順番で動いているからですね。  
  
苦しいですがrenderの前後にtimezoneを変更するpatchをあてます。  

```ruby
# config/initializers/grape_jbuilder_patch.rb
module GrapeJbuilderPatch
  def call
    Time.zone = endpoint.current_user.time_zone # ユーザーのtimezoneを使う
    result = super
    Time.zone = Rails.configuration.time_zone # 戻す
    result
  end
end

module Grape
  module Formatter
    class Jbuilder
      prepend GrapeJbuilderPatch
    end
  end
end
```
