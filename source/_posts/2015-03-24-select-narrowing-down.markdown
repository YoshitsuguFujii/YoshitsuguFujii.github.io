---
layout: post
title: "同一のselectボックスを複数設置した場合に、選択された項目を他のプルダウンから削除するjs"
date: 2015-03-24 14:39:06 +0900
comments: true
categories: [javascript]
---

作成したのはだいぶ前ですが、先日昔お世話になった人からselect-narrowing-downを使いたいんだけどという有難い言葉をいただき、そういえば作ったなぁと思い見返してみました。  
  
  
デモはこんな感じです。  

<!-- more -->

<script src="https://rawgit.com/YoshitsuguFujii/select-narrowing-down.js/master/src/jquery-1.8.3.min.js"></script>
<script src="https://rawgit.com/YoshitsuguFujii/select-narrowing-down.js/master/src/select-narrowing-down.js"></script>
<script type="text/javascript">
  $(function(){
      $("#narrowing_down").bind("selectNarrowingDown", function(){
      var selected_index = $('this').context.activeElement.selectedIndex;
      var selected_text = $('this').context.activeElement[selected_index].text;
      alert(selected_text + "が選択されました");
    })
  });
</script>

**select-narrowing-down.jsのデモ**

結婚相手に求めるものを３つ選んでください
<div id="narrowing_down">
  <select>
    <option value="">--</option>
    <option value="1">お金</option>
    <option value="2">年齢</option>
    <option value="3">容姿</option>
    <option value="4">相性</option>
    <option value="5">性格</option>
  </select>

  <select>
    <option value="">--</option>
    <option value="1">お金</option>
    <option value="2">年齢</option>
    <option value="3">容姿</option>
    <option value="4">相性</option>
    <option value="5">性格</option>
  </select>

  <select>
    <option value="">--</option>
    <option value="1">お金</option>
    <option value="2">年齢</option>
    <option value="3">容姿</option>
    <option value="4">相性</option>
    <option value="5">性格</option>
  </select>
</div>
<br>
<br>
  
  

プルダウンを選択した時に処理を入れれるように第2引数にfunctionを設定できます。  
便利ですね！デモでは選択した内容をalert表示しています  
  
チェックボックスじゃなくてどうしてもプルダウンでやりたい人はよかったら使ってみてください。  

