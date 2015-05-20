---
layout: post
title: "nested_formで下じゃなくて上に要素を追加したい場合"
date: 2015-05-20 18:03:26 +0900
comments: true
categories: [ruby,ruby on rails]
---
  
neste_formは可変なサイズの子要素を動的に追加できるGemです。  
  
[nested form](https://github.com/ryanb/nested_for://github.com/ryanb/nested_form)  
  
いつも大変お世話になっておりますが、今回の案件で追加する先を指定したエレメントの下ではなく上に追加しなければいけなくなり対応しました。  
  
<!-- more -->

バージョンは  
```ruby Gemfile.lock
nested_form (0.3.2)  
```
での作業となります。  
  
ますnested_formで使われるjsを眺めるとそんなに長くない処理であることがわかります。  
その中で追加ボタンを押したときに要素を追加する部分はこの辺りになるようです。  
  
```js jquery_nested_form:55行目あたり
    insertFields: function(content, assoc, link) {
      var target = $(link).data('target');
      if (target) {
        return $(content).appendTo($(target));
      } else {
        return $(content).insertBefore(link);
      }
    },
```
  
ここを上書きします。   
application.jsで`//= require jquery_nested_form`の後に以下の処理を書いて上書きしちゃいましよう。  
  
```js
window.NestedFormEvents.prototype.insertFields = function(content, assoc, link) {
  var target = $(link).data('target');
  if ($(link).data('destination') == "prepend"){
    return $(target).prepend($(content));
  }else{
    if (target) {
      return $(content).appendTo($(target));
    } else {
      return $(content).insertBefore(link);
    }
  }
}
```
  
linkのdata-destination=prependで対象の要素の上にprependで追加されるイメージです。  
  
```erb
<table id="tasks">
  <%= f.fields_for :tasks, :wrapper => false do |task_form| %>
    <tr class="fields">
      <td><%= task_form.text_field :name %></td>
      <td><%= task_form.link_to_remove "Remove this task" %></td>
    </tr>
  <% end %>
</table>
<p><%= f.link_to_add "Add a task", :tasks, :data => { :target => "#tasks", :prepend => true } %></p>
```
  
簡単でした。
