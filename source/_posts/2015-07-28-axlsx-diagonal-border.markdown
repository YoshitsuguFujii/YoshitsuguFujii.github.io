---
layout: post
title: "Axlsxでセルの中の斜線を引く"
date: 2015-07-28 18:55:24 +0900
comments: true
categories: [ruby, gem, axlsx, excel]
---
  
またまた中々見つけられなかったテクニック。  
セルの内部に斜線をひくです。  
  
連日の調査で疲れます。  
  
<!-- more -->
さくっと結論です。  
edgesにdiagonalを指定して、diagonal_upかdiagonal_downにtrueを指定するです。  
edgesにdiagonalを指定しろなんてどこにも書いてなかったので大変でした。  
  
コードにするとこうです。 
  
```ruby
require 'axlsx'

package = Axlsx::Package.new
sheet = package.workbook.add_worksheet(name: 'lists')

diagonal_up = sheet.styles.add_style(
                              {
                               :border => {
                                 :edges => [:diagonal],
                                 :color => "000000",
                                 :style => :thin,
                                 diagonal_up: true
                               }
                              }
                            )
diagonal_down = sheet.styles.add_style(
                              {
                               :border => {
                                 :edges => [:diagonal],
                                 :color => "000000",
                                 :style => :thin,
                                 diagonal_down: true
                                }
                              }
                            )

diagonal_both = sheet.styles.add_style(
                              {
                               :border => {
                                 :edges => [:diagonal],
                                 :color => "000000",
                                 :style => :thin,
                                 diagonal_down: true,
                                 diagonal_up: true
                               }
                              }
                            )


sheet.add_row(['斜線1'], style: diagonal_up)
sheet.add_row([''])
sheet.add_row(['斜線2'], style: diagonal_down)
sheet.add_row([''])
sheet.add_row(['斜線3'], style: diagonal_both)

package.serialize('test.xlsx')
```
  
![Axlsx border diagonal](/images/blog/2015-07-28.png) 
  
もしくはこれでもいけます。  
  
```ruby
require 'axlsx'

package = Axlsx::Package.new
sheet = package.workbook.add_worksheet(name: 'lists')

diagonal_style = sheet.styles.add_style(
                              {
                               :border => {
                                 :edges => [:left, :right],
                                 :color => "000000",
                                 :style => :thin 
                               }
                              }
                            )

br = package.workbook.styles.borders[package.workbook.styles.cellXfs[diagonal_style].borderId]
br.diagonalUp = true
br.diagonalDown = true
br.prs << Axlsx::BorderPr.new(
  name: :diagonal,
  color: Axlsx::Color.new(rgb: '000000'),
  style: :thin,
)


sheet.add_row(['斜め線をひく'], style: diagonal_style)

package.serialize('test.xlsx')
```
