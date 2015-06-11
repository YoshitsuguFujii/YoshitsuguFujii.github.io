---
layout: post
title: "simple_formで表示されるエラーをfull_messagesのものにする"
date: 2015-06-11 14:30:19 +0900
comments: true
categories: [ruby,ruby on rails]
---
  
```ruby
#開発環境
rails (4.2.1)
simple_form (3.1.0)
```
  
simple_formをそのまま使うとエラー発生時のエラーの表示がfull_messageではなく、object.errors[:name]みたいにした時の値しか表示してくれない。  
  
こんな感じ。  
![normal error](/images/blog/simple_form_error2015-06-10.png)  
  
ちょっとこれだとあんまりなのでfull_messageでメッセージを表示したい  
<!-- more -->
この[issue](https://github.com/plataformatec/simple_form/issues/989)でこの問題にパッチを当てている人がいたのでそのままパクらせていただきます。  
  
こうするだけ  
```ruby config/initializer/simple_form_error_path.rb
module SimpleForm::Components::Errors
  def errors_on_attribute
    object.errors.full_messages_for(attribute_name)
  end
end
```
  
するとこうなります。  
![full error](/images/blog/simple_form_full_error2015-06-10.png)  
いい感じですね。  

