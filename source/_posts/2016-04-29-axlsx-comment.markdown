---
layout: post
title: "Axlsxでコメント表示"
date: 2016-04-29 14:50:47 +0900
comments: true
categories: 
---

Axlsxで常に表示されているコメントとマウスオーバーで表示されるコメント


![Axlsxコメント](/images/blog/2016-04-29 14.53.19.png)

<!-- more -->

```ruby
require 'axlsx'
package = Axlsx::Package.new
package.use_shared_strings = true
sheet = package.workbook.add_worksheet(name: 'lists')

sheet.add_row(['品名', '単価', '数量', '計'])
sheet.add_row(['にんじん',    80, 1,      '=B2*C2'])
sheet.add_row(['たまねぎ',    50, 2,      '=B3*C3'])
sheet.add_row(['じゃがいも',  40, 2,      '=B4*C4'])
sheet.add_row(['牛肉',       200, 1,      '=B5*C5'])
sheet.add_row(['カレー粉',   150, 1,      '=B6*C6'])
sheet.add_row(['',            '', '総計', '=SUM(D2:D6)'])

#sheet.add_comment :ref => 'A1', :text => 'penut machine', :author => 'crank', :visible => true
sheet.add_comment(:ref => 'A1', :author => 'Bob', :text => 'Yes We Can!').tap do |comment|
  comment.vml_shape.top_row = 2      # 位置調整用
  comment.vml_shape.left_column = 2  # 位置調整用
  comment.vml_shape.bottom_row = 2   # 位置調整用
  comment.vml_shape.right_column = 4 # 位置調整用
end
sheet.add_comment :ref => 'A6', :text => 'rust bucket', :author => 'PO', :visible => false

package.serialize('test.xlsx')
```
