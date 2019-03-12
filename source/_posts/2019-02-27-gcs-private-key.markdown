---
layout: post
title: "GCSとActiveStorageを使った時に秘密鍵でエラー"
date: 2019-02-27 16:57:03 +0900
comments: true
categories: [ruby,ruby on rails, google cloud platform, gcp]
---

サービスアカウントを発行して、キーを発行してその中身を  
  
```  
bundle exec rails credentials:edit    
```  
  
してstorage.ymlで読み出そうとした時にprivate_keyに改行があるためにうまくいかなかった。  

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

dumpを使えばおっけー  

```
Rails.application.credentials.gcs[:private_key].dump
```

全体はこう。  

```
google:
  service: GCS
  credentials:
    type: "service_account"
    project_id: <%= Rails.application.credentials.gcs[:project_id] %>
    private_key_id: <%= Rails.application.credentials.gcs[:private_key_id] %>
    private_key: <%= Rails.application.credentials.gcs[:private_key].dump %>
    client_email: <%= Rails.application.credentials.gcs[:client_email] %>
    client_id: <%= Rails.application.credentials.gcs[:client_id] %>
    auth_uri: "https://accounts.google.com/o/oauth2/auth"
    token_uri: "https://accounts.google.com/o/oauth2/token"
    auth_provider_x509_cert_url: "https://www.googleapis.com/oauth2/v1/certs"
    client_x509_cert_url: <%= Rails.application.credentials.gcs[:client_x509_cert_url] %>
  project: ''
```

参考
https://github.com/rails/rails/blob/master/guides/source/active_storage_overview.md


