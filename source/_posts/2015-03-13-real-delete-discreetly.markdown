---
layout: post
title: "関連先が存在していたら論理削除、関連先が存在していなかったら物理削除"
date: 2015-03-13 22:21:31 +0900
comments: true
categories: [ruby]
---

という内容のものが業務で必要になり、処理を書きましたが勘違いでそんな仕様はどこにもなくお蔵入りになってしまって悔しいので
ここでさらします。

Gistではここに置いてあります。  
[real_delete_discreetly](https://gist.github.com/YoshitsuguFujii/e86d1f208a594ae4effa)

本体です
```ruby 
module RealDeleteDiscreetly
  extend ActiveSupport::Concern
 
  def delete_discreetly
    relation_exist = false
    self.class.confirm_relations.each do |confirm_relation|
      if destroyed_by_association
        if destroyed_by_association.active_record == confirm_relation.to_s.classify.constantize
          relation_exist = false
          break
        end
      else
        if reflection = self.class.reflections[confirm_relation].presence
          send_method = case reflection.macro
                    when :has_many
                      :exists?
                    else
                      :present?
                    end
 
          if send(confirm_relation).send(send_method)
            relation_exist = true
            break
          end
        else
          raise ArgumentError, "#{confirm_relation} is not Reflection"
        end
      end
    end
 
    unless relation_exist
      self.class.delete_all!(self.class.primary_key.to_sym => id)
    end
  end
 
  module ClassMethods
    def real_delete_discreetly(options = {})
      class_attribute :confirm_relations
      self.confirm_relations = options.delete(:confirm_relations)
 
      acts_as_paranoid options
      after_destroy :delete_discreetly
    end
  end
end
```

論理削除部分はact_as_paranoidに依存しています。  
このソースはlibなどのオートロードの対象となるディレクトリに置く想定です。

該当のモデルでreal_delete_discreetlyすると  
関連先が存在していたら論理削除、関連先が存在していなかったら物理削除  
という動きをするようになるはずです。 

こんな書き方になります。

```ruby
class UserProfile < ActiveRecord::Base
  belongs_to :user
  real_delete_discreetly column: :del_flg, column_type: 'boolean', confirm_relations: [:user]
end
```

こうするとuserが存在する場合は論理削除。  
userレコードが存在しない場合は物理削除になります。  
confirm_relations以外の値はacts_as_paranoidで渡すものと同じものが指定できます。  
今回の例では論理削除のカラムをデフォルトのdeleted_atじゃなくてdelete_flagで見ている例ですね。

rspecも書くつもりだったので各モデルで使えるshared_exampleもあります。

```ruby
shared_examples_for 'real delete discreetly' do |opts|
  let(:base_instance) { opts[:base_instance] }
  let(:confirm_relations) { opts[:confirm_relations] }
  let(:paranoid_column) { opts[:paranoid_column] || :delete_flag }
  let(:paranoid_column_type) { opts[:paranoid_column_type] || :boolean }
 
  before do
    @model = base_instance.dup
    @model.save
    case paranoid_column_type.to_sym
    when :boolean
      @from = false
      @to = true
    else
      time = Time.now
      Time.stub(:now).and_return(time)
      @from = nil
      @to = time
    end
  end
 
  context 'relation exists' do
    before do
      confirm_relation = confirm_relations.first
      case @model.class.reflections[confirm_relation].macro
      when :has_many
        @model.send(confirm_relation).send(:build)
      else
        @model.send("build_#{confirm_relation}")
      end
    end
 
    it 'soft deleted' do
      expect { @model.destroy }.to change { @model.send(paranoid_column) }.from(@from).to(@to)
    end
 
    it 'not real deleted' do
      expect { @model.destroy }.to change(@model.class.unscoped, :count).by(0)
    end
  end
 
  context 'when relation not exists' do
    it 'real deleted' do
      expect { @model.destroy }.to change(@model.class.unscoped, :count).by(-1)
    end
  end
end
```

これをspec_helperなどからrequireするなり読み込めるようにしてもらって  
対象のモデルで

```ruby
require 'rails_helper'
 
RSpec.describe UserProfile, type: :model do
 include_examples 'real delete discreetly',
 base_instance: UserProfile.create(user_id: nil_or_exist_id),
 confirm_relations: [:user]
end
```

とかやると論理削除と物理削除のテストが走ります。  
ただ冒頭でも述べた通りお蔵入りしたのでちゃんと動くかどうか細かい所までは見きれていないです。
何かの参考になれば幸いです。

親のレコードが消された時に連動してdependent: :destroyとかの指定で消える場合は、親のレコードってdestroyed_by_associationに格納されるんですね。  
今回のコードを書いてて初めて知りました。
