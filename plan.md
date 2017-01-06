# 技術範疇

1. 基於Office Open XML技術（ECMA-376）
   1. 考慮使用微軟官方OpenXML SDK
   2. 或是直接剖析文件
2. 基於HTML Canvas 2D Context技術

# 預計的文章標題

1. 01 - 專案目標與相關技術資訊
2. 02 - pptx的文件結構
   1. 使用PowerPoint建立pptx文件的過程
      1. 新增簡報
      2. 套用「佈景主題」
      3. 新增簡報
      4. 套用版面配置
      5. 檢視母片
   2. 觀察pptx的內容
      1. pptx的檔案格式zip archive
      2. _rels/.rels
      3. [Content_Types].xml
      4. docProps/app.xml
      5. docProps/core.xml
      6. docProps/thumbnail.jpeg
      7. ppt/presentation.xml
      8. ppt/viewProps.xml
      9. ppt/presProps.xml
      10. ppt/tableStyles.xml
      11. ppt/slides（投影片）
      12. ppt/slideLayouts（版面配置）
      13. ppt/slideMasters（母片？）
      14. ppt/theme（佈景主題）
      15. ppt/media（圖檔都在這）
3. 03 - Canvas 2D Context繪圖的基礎：圖片、圖形與文字繪製
   1. 把圖片繪製到Canvas
   2. 把圖型繪製到Canvas
   3. 把文字繪製到Canvas
4. 04 - 透過Open XML SDK存取pptx
   1. 關於資料剖析的初步想法
   2. 我的開發環境
   3. 參考文件
   4. 先動手寫個Probe
   5. 使用OpenXML SDK的優缺點
5. 05 - 單位的換算
   1. 長度
   2. 字型大小
   3. 百分比
6. 06 - 使用node.js取得pptx資訊 - 初步剖析
   1. zip-stream
   2. xml2js
   3. 輸出JSON
7. 07 - 動畫的基礎
   1. 使用requestAnimationFrame
   2. 簡單的例子
   3. 需要的改進
8. 08 - 使用node.js取得pptx資訊 - 建立檔案關連列表
   1. 調整node-pptx，產出單一JSON檔，並輸出媒體檔
   2. 開始剖析，先建立檔案關連列表
   3. 下一步
9. 09 - 動畫改進
   1. 加入弧線移動動畫
   2. 加入加減速效果
   3. 初步處理簡報縮放
   4. 其他問題
10. 10 - 從剖析presentation.xml開始
11. 後續的程式架構
   2. 剖析方法
   3. 建立測試
12. 11 - 試做轉場特效
13. 調整程式，逐步套用剖析出來的資料結構
   2. ECMA-376定義的轉場特效
   3. 實作方式
   4. 成果
14. 12 - 剖析佈景主題
15. 目標
   2. 測試
   3. 下一步
16. 13 - 複雜的timing結構
17. timing的用途
   2. node tree的結構
   3. 思考後續剖析及觸發的機制
18. 14 - 剖析母片資訊
19. 母片資訊的剖析重點
20. 15 - 剖析投影片中的資訊
21. 跳過版面配置
   2. 列表外觀
   3. 文字方塊
22. 16 - 文字呈現的細節
23. 文字主體(p:txBody)、段落(a:p)與文字展現(a:run)
   2. 段落屬性
   3. 文字展現屬性
   4. 使用Canvas正確繪製文字
      1. 圖形位置的考量
      2. 圖形四周的留空（padding）
      3. 段落的考量
      4. 文字展現的考量
      5. 折行
24. 17 - 大危機...
25. xml2js的問題
   2. 解決方式
   3. 重寫parser...
26. 18 - 重寫pptx parser
27. 19 - 畫出標準投影片
28. 20 - 投影片的資料架構
29. 21 - 再次剖析母片
30. 22 - 用剖析出的資料試畫母片
   1. 目前產出的母片資料
   2. 試著畫出母片
   3. 繼續調整
31. 23 - 剖析佈景主題及版面配置
32. 24 - 剖析投影片
   1. 修正版片配置的剖析
   2. 剖析投影片
33. 25 - 繼續改進動畫呈現
34. 26 - 進一步改善文字呈現
35. 27 - 理解圖形繪製中的guide語法
36. 28 - 系統內建圖形
37. 29 - 
38. 30 - 
39. ​
40. ​