---
layout: post
title: "強引にGoのメールでverpに対応する"
date: 2016-08-24 18:19:24 +0900
comments: true
categories: [go, golang, postfix, verp]
---
  
Golangで[VERP](http://www.postfix-jp.info/trans-2.2/jhtml/VERP_README.html)対応したくて色々と調べましたが  
強引にやる方法しか思いつかなかったけどせっかく調べたので記録する。  
ソースはこちらを参考にさせていただきました(感謝)  
[Golang Cafe #16 まとめ その2 smtpパッケージ](http://d.hatena.ne.jp/taknb2nch/20140213/1392277157)  
  
verpって単一の宛先のみでカンマ区切りだったりccだったりbccだったりは対応できないっぽいです。  
送るなら1メール1ユーザにしないとダメ？
  
<!-- more -->
  
## postfix設定 
main.cf
/etc/postfix/main.cf 

```
recipient_delimiter = +
default_verp_delimiters = -=
smtpd_authorized_verp_clients = $mynetworks
```
  
以下sendMailはsmtp.SendMailと引数を一緒にしています。  
  
```
func sendMail(addr string, auth smtp.Auth, from string, to []string, msg []byte) error {
	var client *smtp.Client
	var err error

	if client, err = smtp.Dial(addr); err != nil {
		fmt.Fprintf(os.Stderr, "Error: %v\n", err)
		os.Exit(1)
	}

	defer client.Close()

	if err = client.Hello("localhost"); err != nil {
		fmt.Fprintf(os.Stderr, "Error: %v\n", err)
		os.Exit(1)
	}

	if auth != nil {
		if err = client.Auth(auth); err != nil {
			fmt.Fprintf(os.Stderr, "Error: %v\n", err)
			os.Exit(1)
		}
	}

	for _, addr := range to {
		if err = client.Reset(); err != nil {
			fmt.Fprintf(os.Stderr, "Error: %v\n", err)
			os.Exit(1)
		}

		if err = mail(client, from); err != nil {
			fmt.Fprintf(os.Stderr, "Error: %v\n", err)
			os.Exit(1)
		}

		if err = client.Rcpt(addr); err != nil {
			fmt.Fprintf(os.Stderr, "Error: %v\n", err)
			os.Exit(1)
		}

		var w io.WriteCloser

		if w, err = client.Data(); err != nil {
			fmt.Fprintf(os.Stderr, "Error: %v\n", err)
			os.Exit(1)
		}

		if _, err = w.Write([]byte(msg)); err != nil {
			fmt.Fprintf(os.Stderr, "Error: %v\n", err)
			os.Exit(1)
		}

		w.Close()
	}

	if err = client.Quit(); err != nil {
		fmt.Fprintf(os.Stderr, "Error: %v\n", err)
		os.Exit(1)
	}

	return nil
}

func mail(c *smtp.Client, from string) error {
	cmdStr := "MAIL FROM:<%s> XVERP"

	// 8BITMIMEは考慮しない

	_, _, err := cmd(c, 250, cmdStr, from)
	return err
}

func cmd(c *smtp.Client, expectCode int, format string, args ...interface{}) (int, string, error) {
	id, err := c.Text.Cmd(format, args...)
	if err != nil {
		return 0, "", err
	}
	c.Text.StartResponse(id)
	defer c.Text.EndResponse(id)
	code, msg, err := c.Text.ReadResponse(expectCode)
	return code, msg, err
}
```
