---
layout: post
title: "重複している画像ファイルを削除(rubyで)"
date: 2015-03-20 17:27:16 +0900
comments: true
categories: [ruby]
---

重複しているファイルを削除します。

```ruby delete_duplicate_file.rb
require 'digest/md5'

file_hash = ARGV.inject(Hash.new) do |hash, file|
              hash[file] = Digest::MD5.hexdigest(File.open(file, 'rb').read)
              hash
            end

duplicated_md5 = file_hash.values.sort.inject(Hash.new) do |file_count, md5|
                   file_count[md5] = file_hash.values.count(md5)
                   file_count
                 end.select do |md5, count|
                   count > 1
                 end.keys

duplicated_md5.map do |file_md5|
  # 削除ではなく重複したファイルを他のディレクトリに退避したい場合などはここを変えてください。
  File.unlink file_hash.invert[file_md5]
end
```
<!-- more -->

使い方
```bash
ruby delete_duplicate_file.rb ./* 
```

引数でブロブ渡したら勝手に展開されて渡されてきたので、そのままプログラム内で参照しちゃったけどひょっとしたらzshだけかも(?)  
もっとうまく書けるような気もするけど体力不足の今日の私の脳みそではこれが限界。
