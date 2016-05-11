---
layout: post
title: "複数サーバーssh同時操作"
date: 2016-05-11 12:12:31 +0900
comments: true
categories: [tmux]
---
  
冗長構成となっている本番環境でlogを調べたい時とかそれぞれのサーバーにsshしてはいってそれぞれのサーバーで同じコマンド打つのが面倒くさい。  
tmuxを使っていれば複数ペインで同時操作できます。  
  
<!-- more -->  
  
  
.tmux.confに設定追加  
  
```  
# ペイン同時操作  
set-option -g synchronize-panes off  
bind e setw synchronize-panes on  
bind E setw synchronize-panes off  
```  
  
これで  
  
ペイン分割して一つ目のペインでサーバーAへ接続  
二つ目のペインでサーバーBへ接続します。  
  
そのあとCTRL + eでtmuxにコマンドを送るとキーボード入力が２つのペインに送られるようになるので  
しかるべきlogフォルダまで移動してlogを見ればいいですね。  
同期を解除したい場合はCTRL + Eで解除できます。  
