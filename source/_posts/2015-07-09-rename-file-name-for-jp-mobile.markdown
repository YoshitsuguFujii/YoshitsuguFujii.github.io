---
layout: post
title: "ファイル名に_smart_phoneをつけて複製"
date: 2015-07-09 12:09:42 +0900
comments: true
categories: [ruby,ruby on rails]
---

スマートフォンサイトの表示に[jpmobile](https://github.com/jpmobile/jpmobile)を使ってます。  
これはこのようにApplicationcontrollerとかに書くと
  
```ruby
class ApplicationController < ActionController::Base
  include Jpmobile::ViewSelector
end 
```
<!-- more -->
  
PCからのアクセスの場合にはindex.html.erbを表示し  
スマートフォンからのアクセスの場合にはindex_smart_phone.html.erbを表示してくれるという機能を使っています。  
  
今回先にPCサイトがあり、それのスマフォサイトを作るにあたってPCのファイルを上記命名規則に則ってコピーする必要があったのでスクリプト書きました。  
  
```ruby convert_smartphone_name
#! /usr/bin/env ruby
require 'fileutils'

def print_green(str)
  puts "\e[32m#{str}\e[0m"
end

def print_red(str)
  puts "\e[31m#{str}\e[0m"
end

def print_yellow(str)
  puts "\e[33m#{str}\e[0m"
end

if ARGV[0].nil?
  print_red "対象ディレクトリへのパスを指定してください"
end

Dir.glob('*').each do |file|
  next if FileTest::directory?(file)
  dest = file.split(".").inject(String.new) do |str, chunk|
    if chunk == "html"
      str << "_smart_phone"
    end

    if str == ""
      str << "#{chunk}"
    else
      str << ".#{chunk}"
    end
  end

  print_yellow "convert #{file} to #{dest}"
  FileUtils.cp(file, dest)
end

print_green "done."
```
  
これを実行可能なところにおいて、chmod +xで実行可能にしてもらって、  
convert_smartphone_name [対象ディレクトリのパス]  
とかってやれば対象ディレクトリ内のファイルが全部_smart_phone付きでコピーされるはずです。  
