---
layout: post
title: "S3に直接にファイルアップロード"
date: 2018-02-14 18:35:13 +0900
comments: true
categories: [ruby,ruby on rails,s3,carrierwave]
---


Herokuでの大きいファイルアップロードはリクエストタイムアウトが30秒に設定されているのでやっかいです。  

Herokuの公式でも4MBを超えるファイルをあげる場合はS3に直接あげてねって書いてあります。  
https://devcenter.heroku.com/articles/s3#direct-upload  
  
```
This is the preferred approach if you’re working with file uploads bigger than 4MB. The idea is to skip the hop to your dyno, making a direct connection from the end user browser to S3. While this reduces the processing required by your application it is a more complex implementation and limits the ability to modify (transform, filter, resize etc…) the file before storing in S3.
```
  
carriwaveを使っているのでcarriwave_directいいなと思いましたが、  
[carrierwave_direct](https://github.com/dwilkie/carrierwave_direct)  
  
最終更新日が結構前でメンテされていないのかな？ってのとgemを使いすぎるとわけわかめになるので自力でやることにしました。  
  
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

署名付きアップロード用のリンクを生成してjsに渡しjsでそのurlにアップロードする流れです。  
  
Awsコンソールにログインしてs3からBucketを作成します。  
作成したBucketを選択してアクセス権限タブからCORSの設定を開きます。  
以下のように設定します。  

```
<?xml version="1.0" encoding="UTF-8"?>
<CORSConfiguration xmlns="http://s3.amazonaws.com/doc/2006-03-01/">
<CORSRule>
    <AllowedOrigin>*</AllowedOrigin>
    <AllowedMethod>GET</AllowedMethod>
    <AllowedMethod>PUT</AllowedMethod>
    <MaxAgeSeconds>3000</MaxAgeSeconds>
    <AllowedHeader>*</AllowedHeader>
</CORSRule>
</CORSConfiguration>
```
  
次にGemfileに追記してbundle install  
  
```ruby
# Gemfile
gem 'aws-sdk'
```
  
S3_BUCKETを操作できるようにします。  
  
```ruby
# config/initializers/aws.rb
credentials = Aws::Credentials.new(
  ENV[:aws_access_key],
  ENV[:aws_secret]
)

s3_resource = Aws::S3::Resource::new(region: 'bucketのregion', credentials: credentials)
S3_BUCKET = s3_resource.bucket('bucket名')
```
  
URL生成のメソッドはCarrierwaveのuploaderクラスにメソッド生やしました。  
取得はそのままCarrierwaveを使うイメージです。  
今のプロジェクトでは基底クラスを作ってそれを継承させてます。  
  
```ruby
class ApplicationUploader < CarrierWave::Uploader::Base
  def store_dir
    "uploads/#{model.class.to_s.underscore}/#{mounted_as}/#{model.id}"
  end

  def presigned_url(file_name = nil)
    file_name ||= self.model.attributes[mounted_as.to_s]
    object = S3_BUCKET.object([store_dir, file_name].join('/'))
    object.presigned_url(:put, expires_in: 1.minutes.to_i, acl: 'private')
  end
end

class ImageUploader < ApplicationUploader
end
```
  
これで以下のように署名付きリンクを生成できます。  
  
```
user = User.find(1)
user.image.presigned_url # => 署名付きのs3直アップロードurl
```
  
署名付きリンクはデフォルトで900秒（15分）で失効します(上記uploaderでは1分にしている)  
そのためcontrollerに生成してhiddenとかgonで渡すのはやめたほうがいい気がします。  
  
jsで署名付きリンク生成リクエストをもらってurlを返し、そのurlでs3にアップロードするようにします。  
  
Grapeでapiを作ります。  
  
```
params do
  requires :file_name, type: String, desc: 'ファイル名'
end
post do
  user = User.create
  user.update_column('image', params[:file_name])
  {id: user.id, filename: params[:file_name], url: user.image.presigned_url}
end
```
  
carriwaveで処理させたいのですがstore先にidが必要なため保存しています。  
  
以下リンク取得からアップロードまでのサンプルです。  
ちょっと適当な部分があるので動かなかったらすいません。  
  
```js
let fd = new FormData();
fd.append('file', $('.file'));

$.ajax({
  url : 'grapeのurl',
  data : {file_name: fileData.get('file').name},
  type : 'post',
  dataType : 'json'
}).then(function(data) {
  $.ajax({
    url : data.url,
    data : fileData.get('file'),
    type : 'put',
    dataType : 'xml',
    processData : false,
    contentType : false,
    success : function(){
      console.log('成功したよー');
    },
    error : function(){
      console.log('失敗。無念');
    }
  });
});
```
  
carrierwave-attachmentscannerを使っていたんですが、これは直アップロードだと使えないので、保存と同時にafter_saveでsidekiqのキューに押し込んであとでウィルスチェックしています。  


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


**追記**

アットマークやスペースが含まれる場合にs3のファイルが参照できないことがあり調べてみたらクライアントサイドでのファイル名sanitizeが必要みたいです。  
ちなみにcarrierwaveのコードはこちら  
マネしてかいてみるといいと思います。  

https://github.com/carrierwaveuploader/carrierwave/blob/master/lib/carrierwave/sanitized_file.rb#L324-L331



