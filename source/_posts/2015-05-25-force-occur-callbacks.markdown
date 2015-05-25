---
layout: post
title: "モデルのattributeに変更がなくてもnested_attribute_forを使っているモデルでcallbackを発生させる"
date: 2015-05-25 16:15:38 +0900
comments: true
categories: [ruby,ruby on rails]
---

任意の条件でnested_attribute_forの対象の保存処理を走らせる方法です  
  
ファイルのアップロード用のモデルを作っていて`ActionDispatch::Http::UploadedFile`を一旦attr_accessorのアクセサで受けたあとcallbackでファイル名やファイルサイズをmodelクラスの該当のカラムに設定するという処理を書いておりました。  
  
ファイルは可変にアップロードしたかったのでこのモデルの親モデルから参照するようにして、nested_attribute_forで親モデル更新時に一緒に更新するようにします。

```ruby
class Oya < ActiveRecord::Base
  has_many :files, dependent: :destroy

  accepts_nested_attributes_for :files, allow_destroy: true
end
```


```ruby
class File < ActiveRecord::Base
  attr_accessor :file
end
```

<!-- more -->

いらない情報ですが、一応モデルクラスFileモデルの参照先のテーブルfilesテーブルはこんな感じです。
```ruby
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `created_at` datetime DEFAULT NULL,
  `updated_at` datetime DEFAULT NULL,
  `oya_id` int(11) NOT NULL,
  `data_file_name` varchar(255) DEFAULT NULL,
  `data_content_type` varchar(255) DEFAULT NULL,
  `data_file_size` int(11) DEFAULT NULL,
```
  
新規登録の時は良かったんですが、親モデル保存時にnested_attribute_forの対象のモデルクラスのsave(auto_save)やcallbackを走らせるかどうかは実際にデータベースに登録されているカラムの値に変化があったかで決定されているようです(たぶん)  
  
そのため変更時にはformからはfileというアクセサに設定する値のみ飛んでくるような作りだとauto_saveが動かずFileクラスの更新処理が走りませんでした。  
  
そのためchanged_for_autosave?をオーバーライドすることで独自に変更判定を行いcallback並びにauto saveが動くようにします。  
  
```ruby
class File < ActiveRecord::Base
  attr_accessor :file

  def changed_for_autosave?
    if file.present?
      return true
    end
    super
  end
end
```
  
  
知ってると簡単ですが、知らないとはまりますね。
