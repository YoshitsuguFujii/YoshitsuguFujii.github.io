---
layout: post
title: "Axlsxで縦書き"
date: 2015-07-27 18:19:53 +0900
comments: true
categories: [ruby, gem, axlsx, excel]
---
  
Axlsxでセルの内容を縦書きにする方法がわかったので日記に記します。  
セルに角度を付ける方法は見つかったんですが、縦書きにする方法が全然見つからずかなりはまりました。  
  
<!-- more -->
  
VBAだと縦書きはこう設定できますね。  
  
```vbnet
Range("D2").Orientation = xlVertical  
```
  
色々と調べて、xlVerticalの値である-4166をセットしていしてみたりしましたがなかなかうまくゆかず。  
色々と検索して、最終的にcellXfsというキーワードからなぜか[C# vb.netの質問掲示板](http://bbs.wankuma.com/index.cgi?mode=al2&namber=5358&KLOG=15)で発見しましした(圧倒的感謝!)  
textRotationを255を指定すればいけるそうです。  
  
```ruby
require 'axlsx'

package = Axlsx::Package.new
sheet = package.workbook.add_worksheet(name: 'lists')

vertical_text = sheet.styles.add_style(
    {
      alignment: { 
        horizontal: :center,
        vertical: :center ,
        textRotation: 255
      }
    }
)

sheet.add_row(['とっても長いテキスト', '短いテキスト'], style: vertical_text)

package.serialize('test.xlsx')
```
  
できないのかもと本気で諦めかけていたので出来た時には変な声がでてしまいました。  
  
![Axlsx Orientaion xlVertical](/images/blog/2015-07-27.png) 
