---
layout: post
title: "Deviseで特定の条件のみパスワードの検証をスキップする"
date: 2015-06-08 12:35:01 +0900
comments: true
categories: [ruby,ruby on rails, devise]
---

```ruby
#開発環境  
rails 4.2.1  
devise 3.4.1  
```
  
deviseを使っていて、ユーザモデルに仮登録状態を作りたいと思います。  
仮登録の段階ではパスワードはまだ要求されず。  
最後の最後でパスワードを設定して本会員登録完了といったフローです。  
  
deviseでは対象モデルに`devise :validatable`に設定している場合はメールアドレスやパスワードの検証が走ります。  
今回はパスワード以外のチェックはやってほしいため`:validatable`の宣言を外すだけではダメです。  

  
<!-- more -->
  
deviseの以下のソースを見てみます  
  
```ruby lib/devise/models/validatable.rb
def self.included(base)
  base.extend ClassMethods
  assert_validations_api!(base)

  base.class_eval do
    validates_presence_of   :email, if: :email_required?
    validates_uniqueness_of :email, allow_blank: true, if: :email_changed?
    validates_format_of     :email, with: email_regexp, allow_blank: true, if: :email_changed?

    validates_presence_of     :password, if: :password_required?
    validates_confirmation_of :password, if: :password_required?
    validates_length_of       :password, within: password_length, allow_blank: true
  end
end
```
  
パスワードの検証をするかどうかはpassword_required?で判定しています。
判定メソッドはこんな感じです。  
  
```ruby
def password_required?
  !persisted? || !password.nil? || !password_confirmation.nil?
end
```
  
これを対象のモデルでオーバーライドします。  
  
```ruby
class User < ActiveRecord::Base

  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable

  def password_required?
    if member?
      !persisted? || !password.nil? || !password_confirmation.nil?
    else
      false
    end
  end
end
```
  
これでmember?がtrueを返さない(本登録前のユーザ)はパスワードの検証処理をスキップできるようになります。
