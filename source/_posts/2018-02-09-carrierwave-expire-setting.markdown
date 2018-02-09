---
layout: post
title: "carrierwaveのリンクの有効期間を個別に設定"
date: 2018-02-09 19:27:03 +0900
comments: true
categories: [ruby,ruby on rails]
---

全体でかける場合はこうですかね。  

```ruby
# config/initializers/carrierwave.rb
CarrierWave::SanitizedFile.sanitize_regexp = /[^[:word:]\.\-\+]/ # for Japanese
CarrierWave.configure do |config|
  config.fog_credentials = {
    provider:              'AWS',
    aws_access_key_id:     ENV[:access_key],
    aws_secret_access_key: ENV[:secret],
    region:                ENV['s3_region']
  }

  config.fog_public = false
  config.fog_directory = Settings.aws['s3_bucket']
  config.cache_storage = :fog
  config.fog_authenticated_url_expiration = 1.minutes.to_i
end
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

個々のアップローダークラスでかける場合  
```  
class ImageUploader < CarrierWave::Uploader::Base  
  def fog_authenticated_url_expiration  
    1.minutes.to_i  
  end  
end  
```  
uploader内ではmodelでモデル側のインスタンスを参照できるのでmodel側の条件で動的に有効期限を変えるとかもできそうですね。  
