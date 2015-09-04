---
layout: post
title: "Go言語を使ってローカルマシンに簡易botを作ってSlackに投稿"
date: 2015-09-04 17:17:58 +0900
comments: true
categories: [go, golang, slack, bot, slackbot]
---

Goを勉強がてら使いながら簡易slackbotを作ってみました。  
slackにデフォルトで用意されているslackbotってお願いしますをキーに登録すると、「お願いします]
とか「お願いします。」(句読点入り)は反応してくれるけど。 「よろしくお願いします。」みたいに単語をつなげると反応してくれない。  
  
単純に単語が含まれるかだけで反応してくれればいいのに・・・  
  
ということもありお手製slackbotをつくってみました。  
自分のPCでプログラムを動かし、slackで投稿があった際にはOutgoing WebHooksで投稿内容を取得してBotとして返信したいと思います。  
  
slack → ローカルmacのbot → slack  
  
の流れです。  
<!-- more -->
  
まずは外から自分のmacを見つけてもらうためにngrokにトンネルしてもらいましょう。  
ngrokの設定とか概要はこちらを見てださい。  
[ngrokを使用してローカル環境を外部に公開する](http://qiita.com/kitaro729/items/44214f9f81d3ebda58bd)  
普段から最高にお世話になっているサービスです。  
  
Slackの設定です。WebでSlackを開き左のMenuからIntegrationsを選びAll Servicesタブの中からOutgoing WebHooksを選択します。  
新規作成画面のURL(s)にngrokで接続できるurlを設定します。  
channelは新規でtestを。  
Customeize NameにはBotが発言した時の名前が、Cutomize IconにはBotが発言した時のアイコンが設定できます。  
  
これでslackの設定は完了です。  
  
次にgoのプロググラムを動かします。  
もしgoがインストールされてなかったらここ見るといいです。  
[Golang開発環境 3分クッキング](http://qiita.com/morisuke/items/ce04479c1b26d83f4fe3)  
あとyamlを使うのでyamlのライブラリもいれます。  
  
```
go get gopkg.in/yaml.v2
```
  
goのソースはこんな感じ
```go
package main

import (
	"fmt"
	"gopkg.in/yaml.v2"
	"io/ioutil"
	"math/rand"
	"net/http"
	"strings"
	"time"
)

const WordTable = "word.yml"

var p = fmt.Println

func Serve(w http.ResponseWriter, r *http.Request) {
	if r.Method == "POST" {
		text := r.FormValue("text")
		user_name := r.FormValue("user_name")

		if user_name != "slackbot" {
			p("user_name:", user_name)
			return_text := detect_word(text)
			fmt.Fprintf(w, "{\"text\": \"%s\"}", return_text)
		}
	}
}

func getWordTable() map[string][]string {
	buf, err := ioutil.ReadFile(WordTable)
	if err != nil {
		panic(err)
	}

	m := make(map[string][]string)
	err = yaml.Unmarshal(buf, &m)
	if err != nil {
		panic(err)
	}

	return m
}

func keys(words map[string][]string) []string {
	var keys []string

	for key, _ := range words {
		keys = append(keys, key)
	}
	return keys
}

func detect_word(post_text string) (bot_message string) {
	dict := getWordTable()  // 設定を取得
	word_keys := keys(dict) // キーのみ抽出
	var suggestion []string

	for i := 0; i < len(word_keys); i++ {
		if strings.Contains(post_text, word_keys[i]) {
			suggestion = append(suggestion, dict[word_keys[i]]...)
		}
	}

	if len(suggestion) > 0 {
		rand.Seed(time.Now().UnixNano())
		bot_message = suggestion[rand.Intn(len(suggestion))]
	}
	return bot_message
}

func main() {
	http.HandleFunc("/", Serve)
	http.ListenAndServe(":8080", nil)
}
```
  
プログラムの中でソースと同一ディレクトリにあるword.ymlを読み出しています。  
これはslackでの発言と反応をまとめたyamlファイルです。  
発言にひっかけたい文字列をキーに、反応を配列形式で設定します。  
こんな感じです。  
  
```yaml
お願いします:
  - こちらこそよろしく頼む
  - えーっ、やだよ
よろしいでしょうか: &yoro
  - 許さん
  - 金による

いいですか:
  *yoro
```
  
では`fresh`起動しましょう。  
testチャンネルでつぶやいてみます。  
  
![slackbot](/images/blog/2015-09-04.png) 
  
おお、できました！  
ゴルーチンとかチャネルとかまだあんまりよくわかってないので引き続き勉強していきたいと思います。

