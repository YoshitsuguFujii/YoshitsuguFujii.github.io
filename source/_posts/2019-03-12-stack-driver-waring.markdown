---
layout: post
title: "stackdriverを使ったらwarningメッセージがでる"
date: 2019-03-12 15:32:23 +0900
comments: true
categories: [ruby,ruby on rails, google cloud platform, gcp]
---

StackdriverをGemfileのstagingとproduction Groupに追加した後に以下のwarningが表示されるようになった。  

```
Your application has authenticated using end user credentials from Google Cloud SDK.
We recommend that most server applications use service accounts instead.
If your application continues to use end user credentials from Cloud SDK, you might receive a "quota exceeded" or "API not enabled" error.
For more information about service accounts, see https://cloud.google.com/docs/authentication/.
```

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
  
[ここ](https://github.com/googleapis/google-cloud-ruby/blob/master/google-cloud-trace/lib/google/cloud/trace/rails.rb#L131)

```
# Done if Google::Cloud.configure.use_trace is explicitly false
return if Google::Cloud.configure.use_trace == false
```

でreturnしてくれそうだがreturnせずに後続の処理が行われてgcpのcredentialチェックまで走ってしまいエラーになっている模様  
Google::Cloud.configure.use_traceの中身はnilになっているので条件一致せずに後続の処理が実行されている模様。  
falseにしろってコメント書いてあるし。  
  
なので設定してあげる。  
  
```  
# config/environments/development.rbに追記  
Google::Cloud.configure.use_trace = false  
Google::Cloud.configure.use_error_reporting = false  
```  
  
これでwargingはでなくなりました。  
