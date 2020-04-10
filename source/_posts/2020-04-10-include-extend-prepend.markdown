---
layout: post
title: "includeしたmoduleでextendとprepend"
date: 2020-04-10 16:50:31 +0900
comments: true
categories: [ruby,ruby on rails]
---

既存の処理を書き換えつつ、さらに機能追加する。
includeとprependとextendを一個のmoduleで実現するサンプル。

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
# frozen_string_literal: true

module Hoge
  extend ActiveSupport::Concern

  included do
    singleton_class.send(:prepend, Module.new do
      def override
      end
    end
  end

  module ClassMethods
    def class_method
    end
  end

  def instance_method
  end
end
```
