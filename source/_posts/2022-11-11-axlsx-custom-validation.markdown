---
layout: post
title: "Axlsxで半角英数字のみの入力を受け付けたい"
date: 2022-11-11 16:00:07 +0900
comments: true
categories: [ruby,ruby on rails, axlsx]
---

お仕事いただいていたサービスが立て続けにcloseとなってしまいピンチの藤井です。  
お仕事ください！

暇なので[Qiita](https://qiita.com/YoshitsuguFujii/items/e35ef63f3c9162babb14)に記事かいたら全然いいねもらえません。  
いいねください！

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


Axlsxでセルに入力規則で半角英数字のみで何文字以上、何文字以内を設定したので記載

```ruby
p = Axlsx::Package.new
p.workbook.add_worksheet do |ws|

  ws.add_data_validation("A1:A1", {
    :type => :custom,
    :formula1 => 'AND(COUNT(INDEX(FIND(MID(UPPER(A1)&amp;REPT("*",64),ROW($1:$64),1),"ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789"),))=LEN(A1),LENB(A1)&gt;9,LENB(A1)&lt;65)',
    :showDropDown => false,
    :showErrorMessage => true,
    :errorTitle => '入力エラー',
    :error => '半角英数字10文字以上、64文字以内で入力してください',
    :errorStyle => :stop,
    :showInputMessage => true,
    :prompt => '半角英数字10文字以上、64文字以内'})

end

p.serialize 'data_validation.xlsx'
```

<img src="/images/blog/20221111/image.png">    

現場からは以上です。

