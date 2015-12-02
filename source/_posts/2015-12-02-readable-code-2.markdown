---
layout: post
title: "リーダブルコードを意識しながら普段気をつけていること(2)"
date: 2015-11-18 11:33:53 +0900
comments: true
categories: [ruby,readable code]
---
  
普段コードを書く時にリーダブルなコードを書く時に気をつけていることです。  
第１回目は[コンテキストを作る](http://yoshitsugufujii.github.io/blog/2015/11/18/readable-code-1/)でした  
  
第２回目は
  
**早めにパターンを潰す**
  
<!-- more -->
  
何かの処理を追うときおそらく上から順に処理を追って読んでいくと思います。  

読み進める中でユーザがタイプAのユーザだったらどうなのか、利用停止中のユーザだったらどうなるのかということがふと疑問に浮かんだりします。  
その際に読み手は頭の片隅にその疑問を一時格納していて、その処理が書かれているところまで気にしながら読み進むわけです。  
そうなると考慮されていない場合などは最後まで読まなければわかりません。  
  
この疑問を頭の片隅に仮置きしながらコードを読むのはかなりのエネルギーを使うし、ストレスだったりします(少なくとも僕は)  
しかもそれが複雑なシステムでいくつものパターンがある場合は気にしなきゃいけないパターンが膨大になります。  
  
なので読み手の負担にならないように、なるべくパターンは早い段階でリターンするといいですね。  
  
```ruby
class User
  def hard_work
    return unless user.new_commer?
    return unless user.project.burning?
    return unless user.shachiku?

    # 永遠と続くハードワークの長い記述
    do_work(holiday: false)
    do_work(holiday: false)
    do_work(holiday: false)
    do_work(holiday: false)
    do_work(holiday: false)
    do_work(holiday: false)
    do_work(holiday: false)
    do_work(holiday: false)
    do_work(holiday: false)
  end
end
```
  
早い段階で新人と社畜とプロジェクトが炎上していない限りはハードワークは必要ないというのがわかりますね。   
ないとメソッドの最後まで読まないと新人がハードワークしなきゃいけないのかとハラハラしなきゃいけなくなります。  
  
こういう書き方になれると、例えばどこかで新人が精神を病むような強烈な重労働を強いられているという不具合が上がってきた時にもhard_workメソッドは最初に新人はreturnしているので、すぐにここには不具合がないということもわかります。  
