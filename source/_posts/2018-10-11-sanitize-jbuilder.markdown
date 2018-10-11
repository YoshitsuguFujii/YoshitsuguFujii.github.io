---
layout: post
title: "常にjbuilderの値をescape"
date: 2018-10-11 12:27:37 +0900
comments: true
categories: 
categories: [ruby,ruby on rails, jbuilder]
---

jbuliderを使っている時のapiの戻り値、js側でescapeするのかもしれませんが、サーバー側でやりたい時。

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

```ruby
# config/initializers/escape_jbuilder_value
module EscapeJbuilderValue
  def method_missing(*args)
    option = args.extract_options!
    if !::Kernel.block_given? && !option[:raw]
      if args[1].is_a?(String)
        args[1] = Haml::Util.html_safe(ERB::Util.html_escape(args[1]))
      end
    end
    super(*args)
  end
end

class Jbuilder
  prepend ::EscapeJbuilderValue
end
```
HamlつかっているのがHamlと同じescape処理をかけていますが、ここはお好みで。  

```ruby
json.body '<span>raw</hoge>', raw: true
```

とかやるとescapeされずに使えます。  
