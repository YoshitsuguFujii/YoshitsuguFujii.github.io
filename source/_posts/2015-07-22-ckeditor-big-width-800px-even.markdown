---
layout: post
title: "railsでckeditorを使った場合、横幅大きい画像が800pxになってしまう現象"
date: 2015-07-22 12:08:58 +0900
comments: true
categories: [ruby,ruby on rails,ckeditor]
---

[ckeditor](https://github.com/galetahub/ckeditor)でサイズの大きい画像(800px以上の幅の画像)が一様に800pxになってしまう問題。  
gemのckeditorで  
  
```sh
rails generate ckeditor:install --orm=active_record --backend=carrierwavea  
```
  
とかやってそのままデフォで使うと画像の最大サイズが800pxになります。  
  
<!-- more -->  
  
なんでかなーと思って調べてみたら、画像のサイズは生成されるcarrierwaveのuploaderクラスのversion contentの値を使うらしくここが800がデフォルトになっているからでした。  
[ckeditor_picture_uploader.rb#L39](https://github.com/galetahub/ckeditor/blob/master/lib/generators/ckeditor/templates/base/carrierwave/uploaders/ckeditor_picture_uploader.rb#L39)  
  
ここの値を変更することで800px制限を解除できます。私は2400pxとかやって逃げました。  
  
```ruby app/uploaders/ckeditor_picture_uploader.rb
  version :content do
    process resize_to_limit: [2400, 2400]
  end
```
