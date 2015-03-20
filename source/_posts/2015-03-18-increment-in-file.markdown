---
layout: post
title: "ファイル内の特定の行の数値をインクリメントする"
date: 2015-03-18 12:00:03 +0900
comments: true
categories: [ruby]
---

yamlファイルの特定の数字をインクリメントしたくてスクリプトを書きました。
データとしてはこんな感じです。

```yaml
data_one:
  col_num: 1
  name: hoge
  options: fuga
data_two:
  col_num: 1
  name: foo
  options: baa
data_three:
  col_num: 1
  name: foofoo
  options: baabaa
```

<!-- more -->

これのcol_numをインクリメントします。
応用きくように正規表現でひっかかるようにしときましょう。

```ruby
if ARGV.size != 2
  puts "\e[31m#{"[!]引数が不正です。第一引数にはファイルパスを、第二引数には正規表現を指定してください"}\e[0m"
end

file_name = ARGV[0]
regexp    = Regexp.new(ARGV[1])

# 念のためバックアップを取る
`cp #{file_name} #{file_name}_bk`

sequence = nil
replaced_file_body = ""
File.open(file_name) do |file|
  file.read.each_line do |line|
    replaced_file_body << if line =~  regexp
                            # 初回のsequenceのはじまり取得
                            if sequence.nil?
                              sequence = line.gsub(/[^0-9]/, "").to_i
                              line
                            else
                              # sequenceをincrementしながら置換
                              line.gsub(/\d.*/, (sequence += 1).to_s)
                            end
                          else
                            line
                          end
  end

end

# 変換した内容で書き換え
file = File.open(file_name, "w")
file << replaced_file_body
file.close
```

使い方はこんな感じです
```bash
ruby increment_number_in_file.rb official_document_archive.yml col_num 
```

すると結果こうなります。  
  
```yaml
data_one:
  col_num: 1
  name: hoge
  options: fuga
data_two:
  col_num: 2
  name: foo
  options: baa
data_three:
  col_num: 3
  name: foofoo
  options: baabaa
```
  
状況に応じて書き換えながらになると思いますが、よかったら使ってみてください。

