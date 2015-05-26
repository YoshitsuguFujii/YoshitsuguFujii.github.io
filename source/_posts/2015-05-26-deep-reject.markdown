---
layout: post
title: "deep_rejectが欲しくてrefinementsで実装しようとした話"
date: 2015-05-26 19:03:54 +0900
comments: true
categories: [ruby]
---
  
再帰的に条件判定を行うrejectが欲しくなりました。  
こういう動きです。  
  
```ruby
[1,2].deep_reject{|val| val == 1 }  # => [2]

[1,2, [1,2,3]].deep_reject{|val| val == 1 } # => [2, [2, 3]]

[1, 2, {a: "a", b: "b" }].deep_reject{|key, val| key == :a } # => [1, 2, {:b=>"b"}]

[1, 2, {a: "a", b: "b", c: {a: "c", c: "d"} }].deep_reject{|key, val| key == :a } 
# => [1, 2, {:b=>"b", :c=>{:c=>"d"}}]
```
  
かなり便利そうです。  
  
<!-- more -->
refinmetsで実装してみます。  
  
```ruby
  module HashEx
    refine Hash do
      def deep_reject(&block)
        self.each_with_object({}) do |(k, v), result|
          unless block.call(k, v)
            if v.is_a?(Hash)
              result[k] = v.deep_reject(&block)
            else
              result[k] = v
            end
          end
        end
      end
    end
  end

  module ArrayEx
    refine Array do
      def deep_reject(&block)
        self.each_with_object([]) do |v, result|
          unless block.call(v)
            if v.is_a?(Array)
              result << v.deep_reject(&block)
            elsif v.is_a?(Hash)
              result << v.deep_reject(&block) # 呼べない！
            else
              result << v
            end
          end
        end
      end
    end
  end
```
  
ですがこれを
```ruby
using ArrayEx
using HashEx
```
  
と読み込んで使おうとしたところ残念ながら配列の中にハッシュが紛れ込んでいるようなケースだとArrayExのdeep_rejectからHashExのdeep_rejectが参照できず思った通りの結果になりませんでした。  
refinementsはかなり厳密なスコープで動くようです。  
  
まぁあったら有意義なメソッドだしいいかということで結局オープンクラスで実装しました。  
  
```ruby
class Array
  def deep_reject(&block)
    self.each_with_object([]) do |v, result|
      unless block.call(v)
        if v.is_a?(Array)
          result << v.deep_reject(&block)
        elsif v.is_a?(Hash)
          result << v.deep_reject(&block)
        else
          result << v
        end
      end
    end
  end
end

class Hash
  def deep_reject(&block)
    self.each_with_object({}) do |(k, v), result|
      unless block.call(k, v)
        if v.is_a?(Hash)
          result[k] = v.deep_reject(&block)
        elsif v.is_a?(Array)
          result[k] = v.deep_reject(&block)
        else
          result[k] = v
        end
      end
    end
  end
end

```


