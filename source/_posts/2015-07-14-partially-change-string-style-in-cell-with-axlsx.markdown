---
layout: post
title: "Axlsxでセルのなかのテキストに部分的にスタイルを当てる"
date: 2015-07-14 20:28:04 +0900
comments: true
categories: [ruby, gem, axlsx, excel]
---

Axlsxでセルの中の文字にスタイルをあてる方法です 。  
Axlsx::RichTextを使います。  
太字、斜体、打ち消し戦、アンダーライン、色変更に対応しております。  
  
<!-- more -->
  
```ruby
p = Axlsx::Package.new
p.use_shared_strings = true
wb = p.workbook
wrap_text = wb.styles.add_style({:alignment => {:horizontal => :center, :vertical => :center, :wrap_text => true}}  )
rt = Axlsx::RichText.new
rt.add_run('I\'m bold, ', :b => true)
rt.add_run('I\'m italic, ', :i => true)
rt.add_run('I\'m strike' + "\n", :strike => true)
rt.add_run('I\'m bold, italic and strike' + "\n", :b => true, :i => true, :strike => true)
rt.add_run('I\'m style-less :D')
rt.add_run('underlined and red.', :u => :double, :color => 'FF0000')
wb.add_worksheet(:name => "RichText") do | sheet |
sheet.add_row [rt], :style => wrap_text
end
p.serialize 'rich_text.xlsx'
```
  
![完成イメージ](/images/blog/2015-07-14 20.30.19.png) 
  
Axlsxなんでもできますね！
