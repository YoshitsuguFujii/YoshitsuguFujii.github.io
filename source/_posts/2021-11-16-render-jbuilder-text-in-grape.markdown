---
layout: post
title: "grapeでrender_to_string"
date: 2021-11-16 14:08:41 +0900
comments: true
categories: [ruby,ruby on rails, grape, jbuilder]
---

すっかり存在を忘れていたような、あえて忘れていたような。  
お久しぶりです。藤井です。  
  
最近は横須賀のボートばっかり行ってます。カヤックもはじめました。  
プロフィッシュ45にのっています。  
舘山で３回浮かびました。  
出艇ポイントを知らないのでこれから勉強します。  
  
メモを整理していたら走り書きのようなコードがいくつかあって有意義そうなのをのせようかと  
  
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
def render_jbuilder(file_path)  
  root_path =  Rails.root.join 'app', 'views', 'api'  
  engine = ::Tilt.new(root_path / file_path, nil, view_path: root_path)  
  JSON.parse(engine.render(self))  
end  
```  
  
root_path取得のところは  
  
```  
env['api.tilt.root'] = Rails.root.join 'app', 'views', 'api'  
```  
  
の設定をconfig/application.rbとかでやっているのであれば  
  
```  
root_path = env['api.tilt.root']  
```  
  
でも取れます。  
  
現場からは以上です。  
  
  
