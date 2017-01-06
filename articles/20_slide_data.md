# 20 - 投影片的資料架構

為了確實整理好投影片資料，還是回頭從ECMA-376把需要用到的資料做一下整理分析。



## p:presentation (presentation，簡報，型別：CT_Presentation)

以下列出在ppt/presentation.xml中會跟投影片相關的幾個需要處理的資料，這些已經在node-pptx-parser中實作：

- sldMasterIdLst (slide master id list，母片id列表，型別為CT_SlideMasterIdList)：列出母片的id與r:id的對照
- sldIdLst (slide id list，投影片id列表，型別為：CT_SlideIdList)：列出投影片的id與r:id的對照。投影片的播放，會依照這個列表中的順序。
- sldSz (slide size，型別為：CT_SlideSize)：投影片的大小，單位是EMU
- defaultTextStyle (default text list style，型別為：a:CT_TextListStyle)：定義預設的文字列表樣式，包含預設的段落樣式以及一到九級文字列表的樣式。個別的列表樣式定義，型別都是CT_TextParagraphProperties



## p:sldMaster (slide master，母片，型別：CT_SlideMaster)

母片中目前預定要處理的資料有：

* cSld (common slide data，型別為：CT_CommonSlideData)：這是投影片的主要資料存放的地方，從母片、版面配置到投影片都有這個資料
* clrMap (color mapping，型別為：a:CT_ColorMapping)：母片、版面配置、投影片中使用的顏色名稱，會使用這個對照來做轉換，從佈景主題中找出顏色的定義
* sldLayoutIdLst (slide layout id list，型別為：CT_SlideLayoutIdList)：列出版面配置的id與r:id的對照列表
* txStyles (text styles，型別為：CT_SlideMasterTextStyles)：定義文字樣式，部分資料會從簡報中的defaultTextStyle繼承。底下有幾種文字樣式的定義：
  * titleStyle (title style，型別為：CT_TextListStyle)：定義標題文字的樣式，其中個別的列表樣式定義，型別也都是CT_TextParagraphProperties，下同
  * bodyStyle (body style，型別為：CT_TextListStyle)：定義內容文字的樣式
  * otherStyle (other style，型別為：CT_TextListStyle)：定義其他文字樣式





## p:sldLayout (slide layout，版面配置，型別：CT_SlideLayout)

版面配置中需要處理的資料有：

* cSld (common slide data，型別為：CT_CommonSlideData)：母片中也有這個資料
* clrMapOvr (color mapping override，型別為：CT_ColorMappingOverride)：如果顏色對照在版面配置中有調整，會存放在這裡
* type (型別為：ST_SlideLayoutType，是列舉字串的集合)：定義版面配置的類型，例如titleOnly代表只有標題，blank代表空白投影片等等





## p:sld (slide，投影片，型別：CT_Slide)

投影片中需要處理的資料有：

* cSld (common slide data，型別為：CT_CommonSlideData)：母片及版面配置中都有這個
* clrMapOvr (color mapping override，型別為：CT_ColorMappingOverride)：版面配置中也有這個資料
* show：是否要播放這個投影片





## 資料的特性

這裡面會大量重複使用的結構，第一個是CT_TextParagraphProperties。它出現在幾個地方：

1. a:CT_TextListStyle中定義的個別文字樣式，都是用這個型別來定義的
2. a:CT_TextListStyle會在幾個地方使用：
   1. ppt/presentation.xml中的defaultTextStyle
   2. ppt/slideMasters/slideMaster*.xml中的txStyles底下的titleStyle、bodyStyle以及otherStyle
   3. ppt/slideMasters/slideMaster\*.xml（母片）、ppt/slideLayouts/slideLayout\*.xml（版面配置）、ppt/slides/slide\*.xml（投影片）中，每個sp（圖形）中的txBody（文字的最外層容器）底下：
      1. lstStyle
      2. 每個a:p（段落），都有pPr，型別就是CT_TextParagraphProperties

如果在投影片中使用到版面配置中預設的圖形，例如標題或內容文字，資料就會這樣一層一層繼承及覆寫，最後來到段落中的pPr。

如果是製作投影片時才拉的文字方塊，這些屬性就會直接寫在圖形的資料中。

最後彙整到每個圖形中的結構大概是這樣：

1. slide
   1. 多個shape
      1. 1. shape屬性
         2. textBody
            1. textBody屬性
            2. 多個段落
               1. 段落屬性（CT_TextParagraphProperties）
               2. 多個文字展現（Text Run）或分行（Line Break）
                  1. 文字展現屬性（CT_TextCharacterProperties）
      2. pic
         1. pic屬性
         2. blip
            1. blipfill
               1. embed：圖檔的r:id

昨天的範例也大致上是用這樣的結構。最終使用的json結構中，會將簡報預設資料、母片、版面配置的資料全部整合到投影片中的圖形資料中，這樣在繪製投影片時，就不需要一層一層查詢並彙整資料。





## 後續...

把投影片整個資料的架構釐清以後，要怎麼做就很清楚了。接下來，就是依照這個架構把投影片的剖析器完成。

