---
layout: post
title: "pecoでctrl+jとctrl+kで上下移動"
date: 2015-04-20 19:31:45 +0900
comments: true
categories: [peco]
---

pecoをランチャーとして使っているのですが、  
メニューを呼び出した後ホームポジションから手を離して矢印キーに指を運ぶのが嫌でctrl+jとctrl+kで移動できないかと思っていたら会社の人にやり方を教えてもらいできました。

具体的にはpecoの[キーマップ](https://github.com/peco/peco#keymaps)を使います

<!-- more -->


keymap設定用にconfig.jsonを作成します。

```bash
cd ~/
mkdir .peco
vim ~/.peco/config.json
```


以下の内容で保存します。

```json
{
    "Keymap": {
        "C-j": "peco.SelectDown",
        "C-k": "peco.SelectUp"
    }
}
```

これでctrl+jで下に、ctrl+kで上に移動するようになっているはずです。
簡単！
