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
5. 05 - 單位的互換
6. 06 - 使用node.js取得pptx資訊 - 初步剖析
7. 07 - 手動繪製出簡報畫面
8. 08 - 使用node.js取得pptx資訊 - 進一步剖析
9. 09 - 動畫的基礎
10. 10 - 組合佈景主題、母面、版面配置與投影片資訊
11. 11 - 轉場效果
12. 12 - 投影片的JSON資料結構
13. 13 - build list支援
14. 14 - time list支援
15. ​