---
layout: post
title: "rails5でafter_commitが動かなくなってしまった"
date: 2017-10-31 10:27:08 +0900
comments: true
categories: [ruby, ruby on rails]
---
  
ITネタです。すいません。  
railsのモデルデータに変更が加えられたらelasticsearchのデータをafter_commitで更新するようにしているのですが。  
  
```ruby  
after_commit :index_elasticsearch_document, on: [:create, :update]  
after_commit :delete_elasticsearch_document, on: [:destroy]  
```  
  
Rails5.1.4にしたら設定したcallbackが走らなくなってしまいました。  
困っている人結構いるような気がするんですがどうなんでしょうかね。  
  
<!-- more -->  
  
<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>    
<ins class="adsbygoogle"  
     style="display:block; text-align:center;"  
     data-ad-layout="in-article"  
     data-ad-format="fluid"  
     data-ad-client="ca-pub-7039502723411845"  
     data-ad-slot="8206045005"></ins>  
<script>  
     (adsbygoogle = window.adsbygoogle || []).push({});  
</script>  
  
  
onオプションをつけると動かなくて外すと動きます。  
どうもcallbackが処理される過程の[transaction_include_any_action?](https://apidock.com/rails/v4.0.2/ActiveRecord/Transactions/transaction_include_any_action%3F)によるどの操作(insert, update, destroy)での更新かとonオプションによる一致が正常に判定されず動かないようです。  
ぐぐってみるとヒットするのはこのあたりでしょうか。  
  
  
[after_create_commit and after_update_commit alias don't always run when next to each other #29554](https://github.com/rails/rails/issues/29554)  
  
[after_commit doesn't work with optimistic locking #29318](https://github.com/rails/rails/issues/29318)  
  
  
これのうち下のリンクが正解でした。  
修正PRは[これ](https://github.com/rails/rails/pull/29096)ですが、まだマージされてませんね。  
この[コミット](https://github.com/rails/rails/commit/371c083fb0620efebf7bd0188a66486403e12ecc)が原因のようです。
5.0.2ではうごくみたいですw
  
  
ちなみに関わっている案件の場合は正確にはcomposite_primary_keysを使っているのでcomposite_primary_keysの方の悲観的ロックが原因でした。  
メソッドオーバーライドしているのでほぼ同じ現象です。  
  
  
[ここ](https://github.com/composite-primary-keys/composite_primary_keys/blob/master/lib/composite_primary_keys/locking/optimistic.rb#L43)で[PR](https://github.com/rails/rails/pull/29096)と同じように@_trigger_update_callbackを設定すれば治ります。  
  
ただパッチをあてるのもなって感じなのでモデル側でこうなおしました。  
  
  
```ruby  
class ElasticsearchNoModel < ApplicationRecord  
  def _update_record(*args)  
    affected_rows = super  
    if affected_rows == 1  
      @_trigger_update_callback = true  
    end  
  end  
end  
```  
  
create時とupdate時はこれで治りましたがdestroyの時がこれだとまだうごきません。  
ちなみに今関わっている案件ではparanoia使ってます。  
  
[after_commit with on: option doesn't work at Rails 5.1.3 #418](https://github.com/rubysherpas/paranoia/issues/418)  
  
issueあがってますがリアクションありませんね。  
  
ソース読んでこう直しました。  
  
```ruby  
class ElasticsearchNoModel < ApplicationRecord  
  def destroy  
    @_trigger_destroy_callback = true  
    super  
  end  
end  
```  
  
@_trigger_destroy_callbackをsuperの前に設定するのはparanoiaのメソッドがdeleted_atの更新とrun_callbackを同一メソッドでやっているためです(なるべくパッチorメソッドをいじりたくないので先に設定してしまっています)  
  
  
これで無事にafter_commitが動くようになりました。  
