# 02 - pptx的檔案結構

## 使用PowerPoint建立簡報的過程

為了要了解pptx裡面會有哪些東西，首先還是要來操作一下PowerPoint。使用PowerPoint製作簡報的過程，不包含內容細節的話，大概有幾個部分：

1. 新增簡報：按下「Ctrl+n」（Command+n），PowerPoint就會新增一個未存檔的pptx，並且加入一個套用「標題投影片」版面配置的投影片。如果開啟PowerPoint時沒有預先選好「佈景主題」，畫面是單調空白的。

   ![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/02-001.png)

2. 套用「佈景主題」：在佈景主題中選擇一個來使用，可以馬上看到版面有變化。除了加上背景，標題文字框的位置、大小、顏色等都有不同變化。

   ![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/02-003.png)

3. 新增投影片：做好標題投影片後，可以再新增一個投影片

4. 更換「版面配置」：

   1. 在「標題投影片」之後新增的投影片，會預設套用「標題及物件」這個版面配置。

      ![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/02-005.png)

   2. 同時，也可以透過「版面配置」選單來選擇不同的版面配置來套用。

      ![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/02-011.png)

5. 檢視「母片」：

   1. 透過「檢視」中的母片檢視，可以開啟「投影片母片」

      ![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/02-012.png)

   2. 可以選擇不同的「版面配置」來看他的設計

      ![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/02-014.png)

   3. 在「佈景主題」中，可以選擇配色及字型

      ![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/02-015.png)

      ![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/02-016.png)

   4. 可以看到，目前使用的「佈景主題」，是在右側加上兩個「圖案」當做背景，透過滑鼠右鍵可以開啟「格式化圖案」對話框，調整他的配置

      ![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/02-017.png)

   5. 預設的顏色，是從「佈景主題」中的配色挑出來的

      ![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/02-018.png)

6. 存檔：最後，把它存檔。我存檔的檔名是「slide1.pptx」



## 一個簡單的歸納

一個簡報，會包含幾個部分

1. 佈景主題
2. 母片
3. 版面配置
4. 投影片
5. 其他相關資訊



## 實際的pptx檔案格式

pptx的檔案格式...說穿了其實就是個ZIP檔，解壓縮以後會解出許多目錄及檔案，大致上像這樣：

![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/02-009.png)

幾個重要的檔案目錄及檔案類型

1. _rels目錄：裡面會有許多跟上一層目錄中的xml檔搭配的rels檔，他其實還是一個xml格式的檔案，用來描述檔案間的關係。
2. [Content_Types].xml：列舉使用到的檔案類型與xml namespace
3. _rels/.rels：列出幾個根文件，包括docProps/core.xml、docProps/app.xml、ppt/presentation.xml，裡面包含了pptx檔案的一些基本資訊。ppt/presentation.xml裡面會把所有使用到的xml檔都列出來
4. ppt/theme：這個目錄應該會放「佈景主題」相關的檔案。（先用猜的，細節之後再來觀察，後面幾個也是這樣）
5. ppt/slideMasters：這個目錄應該會放「母片」相關的檔案，就是母片檢視中版面配置列出來的第一個，只有透過他才能調整一些背景的元素。
6. ppt/slideLayout：想必是「版面配置」相關的檔案。
7. ppt/slides：各個「投影片」相關的檔案。


## 總之...

接下來的方向就比較清楚了，要繪製一個投影片，我們會需要從「佈景主題」中取得配色、字型等基本資訊，再透過「母片」取得背景的相關資訊及一些文字階層的基本設定，然後從「版面配置」取得各個元素的位置及配置，最後再從「投影片」中取出需要的資料，才能把一個投影片繪製出來。（目前看起來似乎是這樣，日後觀察各個細節才能確定XD）