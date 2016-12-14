# 01 - 專案目標與相關技術資訊

## 緣起

我們原本有做一些SCORM的線上英語教材，製作方式是依據現有的版型與題型，用Excel製作課程的資料，然後套版產生html教材。

最近在規劃明年要做的教材內容，結果請老師提供一些發想，他就提供了一份文法教學的PowerPoint。由於SCORM規範，必須要用html來製作，這樣就有點麻煩XD...因為他的PowerPoint有點複雜，每一個投影片中，都有逐次出現的內容以及動態效果，文字跟圖形會移來移去。原本想透過使用SlideShare或GoogleDoc內嵌到html文件中，但是SlideShare跟GoogleDoc都無法正確呈現這些動態。SlideShare根本不會有動態，GoogleDoc有出現動態，但是並不正確XD

更麻煩的是，這個PowerPoint最精采的部份其實就是這些動態效果，透過他相當清晰的表達出所要講述的文法的意義。如果拿掉的話，整個PowerPoint舊乏善可陳了...

既然是明年才要使用的東西，乾脆自己來做做看吧！所以開始一步一步的摸索。



## 目標

1. 從PowerPoint文件中取出可用資訊，轉成適合Javascript使用的格式，目前考慮轉成Object Literal或JSON
2. 透過這些資訊，把簡報繪製到Canvas中，最好還可以根據目前的螢幕解析度來縮放，甚至用全螢幕的方式播放



## 技術資訊

1. PowerPoint檔案格式剖析
   1. pptx格式使用的是Office OpenXML的公開標準，標準文件可以從ECMA網站取得，他的標準編號是ECMA-376，文件的下載點是：[Standard ECMA-376 - Office Open XML File Formats](http://www.ecma-international.org/publications/standards/Ecma-376.htm)
   2. 微軟有提供一套Office OpenXML的SDK，可以透過他讀取pptx文件，取得內容資訊。參考文件在MSDN：[Welcome to the Open XML SDK 2.5 for Office](https://msdn.microsoft.com/zh-tw/library/office/bb448854.aspx)
   3. 除了一些多媒體內容，pptx裡面是許多xml檔案的集合，所以也可以考慮直接剖析這些xml文件來取得內容資訊
2. Canvas
   1. 使用Canvas 2D Context API來把內容繪製到Canvas中。目前主要會參考標準文件：[HTML Canvas 2D Context](https://www.w3.org/TR/2dcontext/)
   2. 我通常還會參考MDN上的資訊：[CanvasRenderingContext2D](https://developer.mozilla.org/en-US/docs/Web/API/CanvasRenderingContext2D)



## 初步看到的困難點

1. Office OpenXML使用的xml語法，主要的文件是ECMA-376 5th Edition Part 1，內容非常龐大，有五千多頁XD，到底怎樣可以用最快的速度把需要的資訊找出來呢...
2. 雖然都是繪圖，HTML 2D Context API跟Office OpenXML需要的繪圖功能，不知道有多大的差距...



## 總之...

雖然不知道做不做得出來，反正就是做下去了。之後大致上會同時進行兩個主軸的嘗試，一方面看看怎樣剖析pptx文件比較好做，一方面看看需要的繪圖功能怎樣用HTML Canvas 2D Context做出來。可能成功，也可能失敗就是了。
