---
layout: post
title: "railsでuser_idをurlに付与して引き回す方法"
date: 2015-06-10 12:01:49 +0900
comments: true
categories: [ruby,ruby on rails]
---

特定のパラメータを常にurlに付与する方法です。  
  
urlにuser_idをクエリなどにふくめておくとgoogle analyticsとか使った時に、特定のユーザがどういうページにアクセスしたかわかりユーザの行動分析に使えたりします。  
  
なのでurlに自動でuser_id=[:user_id]の形式でアクセスしてるページのuser_idが付与されると非常に便利そうです。  
  
参考になったのはjpmobileのソースでした(docomo端末の場合にguidを自動付与したりしています)  
[docomo_guid.rb](https://github.com/jpmobile/jpmobile/blob/master/lib/jpmobile/docomo_guid.rb)
  
<!-- more -->
  
こうすれば自動でユーザーIDが設定されます。  
  
```ruby app/controller/application_controller.rb 
class ApplicationController < ActionController::Base
  def default_url_options
    result = super || {}
    if current_user.present?
      result.merge!(user_id: current_user.id)
    else
      result.delete(:user_id)
    end
    result
  end
end
```
  
これでログインしていれば(上記だとcurrent_userが取れれば)  
http://localhost:3000/?user_id=6  
みたいに付与されるはずです。  
  
今やっている案件でこの処理が必要になったので思い出しながら書いてみましたが、以前これを利用して遷移前の履歴をurlに保持するgemを作ったことをふと思いだしました。  
[light_stalking](https://github.com/YoshitsuguFujii/light_stalking)  
  
このGEMは思いつきで作って実際に使ってないので色々と問題ありそうですが、default_url_optionsを使うことでurlの最大長にひっかからない範囲で色々なことができそうという一例になるんじゃないかと思いました。
