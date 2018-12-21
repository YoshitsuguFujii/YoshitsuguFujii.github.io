---
layout: post
title: "ExceptionNotificationがある環境でSlackNotifierだけ使う"
date: 2018-12-21 17:53:59 +0900
comments: true
categories: [ruby,ruby on rails]
---

ものぐさネタ。

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

失敗レコード数とかraiseするほどではないんだけど、slackに通知だけしたい時。

```
ExceptionNotifier.class_variable_get('@@notifiers')[:slack].notifier.ping('hogehoge')
```

