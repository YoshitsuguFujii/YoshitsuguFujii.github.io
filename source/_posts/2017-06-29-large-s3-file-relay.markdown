---
layout: post
title: "large_s3_file_relay"
date: 2017-06-29 15:25:15 +0900
comments: true
categories: [ruby, s3, ruby on rails]
---


### 近況とか

最近の藤井は月〜木は新橋の方面で働き金曜日は在宅で仕事させてもらっています。  
言語は相変わらずRubyでFWはRubyOnRailsですね。  
Firebase触ったり、WebRTCに触ったり、はじめてガチでHeroku使ったりしています。  
  
自分が業界に入りたての頃はこういったサービスがなかったので1からどう作るかって感じでしたが、  
最近はどうやってサービスを組み合わせて作るかって感じに変化したように思います。  
サービスの学習コストの方が高めですかね。  
  
Macbook Airの日本語キーボードから英字配列のhhkbに移行しました。  
:が打ちにくくて最初文句ブーブーでしたが、慣れるとたしかに英字配列の方がやりやすい気がします。  
職場と自宅で持ち運びがめんどくさかったので二台買いました。  
hhkbの墨とtype-sです。  
お金使いすぎで鼻血出そうです。  
  
帰宅ランは続けていて調子いいときは10キロぐらい走ります。  
だいたい7、8キロぐらいを目安に走っています。  
江戸川沿い走るの最高に気持ちいいです。  
  
<!-- more -->  
  
### 表題の件
  
S3のファイルをRailsを中継してクライアントにストリーミングダウンロードする処理書きました。  
Httparty使ってます。Httpartyでstreaming download楽ですねー。  

```ruby
class AttachmentController < ApplicationController
  include ActionController::Live

  def download
    attachment = Attachment.find(params[:id])
    begin
      self.response.headers["Content-Type"] = attachment.content_type
      self.response.headers["Content-Length"] = attachment.file_size
      self.response.headers["Content-Disposition"] = "attachment; filename=#{attachment.file_before_type_cast}"
      self.response.headers["Content-Transfer-Encoding"] = "binary"
      self.response.headers["Last-Modified"] = attachment.updated_at.ctime.to_s
      HTTParty.get(attachment.url, stream_body: true) do |fragment|
        self.response.stream.write fragment
      end
    rescue ActionController::Live::ClientDisconnected  # キャンセルされた場合
      # nop
    ensure
      response.stream.close
    end
  end
end
```
  
Puma使いましょう。webrickとかthinは対応していません。  
  
config/environments/development.rbに以下の設定を追加。

```ruby
config.cache_classes = true
config.eager_load = true
```

あとは`bundle exec rails s Puma`して試してみてください。
