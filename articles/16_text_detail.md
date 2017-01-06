# 16 - 文字呈現的細節

弄pptx的剖析有點膩了，今天先來看文字的呈現以及怎麼利用Canvas 2D Context來實作。



## 文字主體(p:txBody)、段落(a:p)與文字展現(a:run)

Office OpenXML與文字呈現、樣式等相關的部份，除了昨天提到的簡報、母片、版面配置到投影片的資料繼承關係，相關的屬性主要區分成三個部分：

1. 文字主體(p:txBody, Text Body)：這裡的屬性，主要是定義文字方向及文字與圖形的間隔等
   1. lIns(left inset), tIns(top inset), rIns(right inset), bIns(bottom inset)，指定文字與外框的間距，單位是EMU
   2. vert：垂直文字的字型方向
   3. rot：旋轉角度
   4. anchor, anchorCtr, fromWrodArt, horzOverflow, vertOverflow...
2. 段落(a:p, Paragraph)
   1. algn(align)：文字對齊方式
   2. defTabSz(default tab size)：預設的tab字元大小
   3. eaLnBrk(East Asian Line Break)：東亞字型的字元是否可以被切開（林=>木木之類）
   4. fontAlgn(font align)：字型的垂直對齊方式。每個段落中的文字展現，可以設定各自的字型大小及樣式，垂直對齊會影響不同文字展現的垂直對齊。
   5. hangingPunct(hanging punctuation)：段落最後的標點，是否可以放到另一行
   6. indent：首行縮排的長度，單位是EMU
   7. latinLnBrk(latin line break)：較長的文字，是否可以拆到兩行中（英文等）
   8. lvl(level)：指定段落的列表級數，單位是zero base，所以第一級的話，數字是0
   9. marL(left margin)：段落的左側邊界長度，單位是EMU，這個數字加上lIns就是段落文字左側開始繪製的位置
   10. marR(right margin)：段落的右側邊界長度，單位是EMU，這個數字加上rIns就是段落右側必須空出來的間隔，如果段落繪製超過這個位置，就需要折行
   11. rtl(right to left)：文字流向是否是右到左（例如阿拉伯文）
3. 文字展現(a:r, Text Run)，每個段落中，包含一至多個Text Run，各自可以有不同的屬性
   1. b(bold)：粗體字
   2. i(italic)：斜體字
   3. kern(kerning)：請自己找，字元間距的調整
   4. kumimoji：垂直文字中的英文字母，是否要垂直或轉九十度
   5. spc(spacing)：字元間距
   6. strike：刪除線
   7. sz(size)：字型大小
   8. u(underline)：底線

## 使用Canvas 2D Context正確繪製

Canvas 2D Context中有幾個屬性及方法，可以協助盡量達成以上目標，不過不是全部XD...有一些設定是CSS還沒完整支援的，在Canvas也可能沒支援。

可以使用的Canva 2D Context屬性及方法有：

1. `ctx.font`：指定字型的屬性，包含
   1. 字型名稱：Arial, 新細明體等等
   2. 是否為斜體：加上italic就是斜體字
   3. 粗細：指定為bold，或是用數字指定，最粗是900
2. `ctx.textAlign`：文字左右對齊的方式，會參考fillText指定的座標
3. `ctx.textBaseline`：指定文字垂直對齊的基線，top, middle, bottom等
4. `ctx.fillText('string', left, top)`：繪出文字
5. `ctx.measureText('string')`：測量指定font樣式繪製出字串的寬度





## 還是有差距...

Canvas 2D Context跟Office OpenXML需要的文字樣式屬性，還是有些差距。例如kerning好像完全沒辦法設定，底線及刪除線沒有直接支援。其他倒是可以透過`measureText`算出前面繪製的文字長度，然後跟各個參數做計算來設法做，不過這樣需要一個字元一個字元繪製，有點麻煩。

先捨棄做不到的的部份，彙整一下然後明天用比較完整的屬性設定來試試繪製文字。