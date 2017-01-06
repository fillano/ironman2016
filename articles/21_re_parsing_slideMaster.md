# 21 - 再次剖析母片



## 與版面配置、投影片同中有異的架構

跟版面配置與投影片一樣，母片中也有common slide data這個最主要的圖形資料集合，但是又各自有自己特有的資料，所以在剖析common slide data的資料的部份可以共用。

雖然還沒完全寫完...不過先看一下母片的剖析方式：

1. 之前已經為了ppt/presentation.xml寫了textParagraphProperty跟textCharacterProperty的剖析程式，現在就加上更多的。
2. 透過slideMasterParser()來剖析母片
   1. 透過masterSlideDataParser()來剖析母片的common slide data
      1. background比較簡單，直接解決
      2. shapes則透過兩種parser來解決：
         1. shapeParser()
            1. id, name, type, spType, presetGeom等直接處理
            2. textBody則透過textBodyParser()來處理
               1. 用textRunParser()來處理段落的文字展現
               2. 用lineBreakParser()來處理段行的標記
               3. 用fldParser()來處理欄位資料
         2. picParser()：處理嵌入圖形的資料
   2. 透過listStyleParser()來剖析母片中txStyles中的titleStyle, bodyStyle以及otherStyle等資料
   3. slideLayoutIdList跟colorMap比較簡單，就直接在slideMasterParser()中搞定





## 跑跑看

還沒寫測試，不過先跑跑看會不會發生嚴重錯誤：

![21-001.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/21-001.png)

嗯，程式會動...不過因為很多東西還沒好，或是只定義了函數名稱，所以覆蓋率有點差XD

再看一下輸出的slideMaster資料：

```json
{
  "background": "bg2",
  "shapes": [
    {
      "id": "2",
      "type": "shape",
      "spType": "title",
      "x": 457200,
      "y": 274638,
      "cx": 7620000,
      "cy": 1143000,
      "presetGeom": "rect",
      "textBody": {
        "paragraphs": [
          {}
        ],
        "vert": "horz",
        "lIns": "91440",
        "tIns": "45720",
        "rIns": "91440",
        "bIns": "45720",
        "rtlCol": "0",
        "anchor": "ctr"
      },
      "name": "Title Placeholder 1"
    },
    {
      "id": "3",
      "type": "shape",
      "spType": "body",
      "x": 457200,
      "y": 1600200,
      "cx": 7620000,
      "cy": 4800600,
      "presetGeom": "rect",
      "textBody": {
        "paragraphs": [
          {
            "lvl": null,
            "algn": "l",
            "defTabSz": 914400,
            "eaLnBrk": true,
            "fontAlgn": null,
            "hangingPunct": "1",
            "indent": -228600,
            "latinLnBrk": false,
            "marL": 342900,
            "marR": null,
            "rtl": false,
            "lnSpc": null,
            "spcBef": 20000,
            "spcAft": null,
            "tabLst": null,
            "defRPr": {
              "altLang": null,
              "b": null,
              "baseline": null,
              "bmk": null,
              "cap": null,
              "dirty": null,
              "err": null,
              "i": null,
              "kern": "1200",
              "kumimoji": null,
              "lang": "zh-TW",
              "noProof": null,
              "normalizeH": null,
              "smtClean": null,
              "spc": null,
              "strike": null,
              "sz": "2200",
              "u": null,
              "ln": null,
              "solidFill": "tx1",
              "effectLst": null,
              "highlight": null,
              "uLn": null,
              "uFill": null,
              "latin": "+mn-lt",
              "ea": "+mn-ea",
              "cs": "+mn-cs",
              "sym": null,
              "hlinkClick": null,
              "hlinkMouseOver": null,
              "rtl": null,
              "extLst": null
            },
            "extLst": null,
            "buClr": "accent1",
            "buSzPts": null,
            "buFont": "Arial",
            "buChar": "•",
            "buNone": null
          },
          {
            "lvl": null,
            "algn": "l",
            "defTabSz": 914400,
            "eaLnBrk": true,
            "fontAlgn": null,
            "hangingPunct": "1",
            "indent": -228600,
            "latinLnBrk": false,
            "marL": 640080,
            "marR": null,
            "rtl": false,
            "lnSpc": null,
            "spcBef": 20000,
            "spcAft": null,
            "tabLst": null,
            "defRPr": {
              "altLang": null,
              "b": null,
              "baseline": null,
              "bmk": null,
              "cap": null,
              "dirty": null,
              "err": null,
              "i": null,
              "kern": "1200",
              "kumimoji": null,
              "lang": "zh-TW",
              "noProof": null,
              "normalizeH": null,
              "smtClean": null,
              "spc": null,
              "strike": null,
              "sz": "2000",
              "u": null,
              "ln": null,
              "solidFill": "tx1",
              "effectLst": null,
              "highlight": null,
              "uLn": null,
              "uFill": null,
              "latin": "+mn-lt",
              "ea": "+mn-ea",
              "cs": "+mn-cs",
              "sym": null,
              "hlinkClick": null,
              "hlinkMouseOver": null,
              "rtl": null,
              "extLst": null
            },
            "extLst": null,
            "buClr": "accent2",
            "buSzPts": null,
            "buFont": "Arial",
            "buChar": "•",
            "buNone": null
          },
          {
            "lvl": null,
            "algn": "l",
            "defTabSz": 914400,
            "eaLnBrk": true,
            "fontAlgn": null,
            "hangingPunct": "1",
            "indent": -228600,
            "latinLnBrk": false,
            "marL": 1005840,
            "marR": null,
            "rtl": false,
            "lnSpc": null,
            "spcBef": 20000,
            "spcAft": null,
            "tabLst": null,
            "defRPr": {
              "altLang": null,
              "b": null,
              "baseline": null,
              "bmk": null,
              "cap": null,
              "dirty": null,
              "err": null,
              "i": null,
              "kern": "1200",
              "kumimoji": null,
              "lang": "zh-TW",
              "noProof": null,
              "normalizeH": null,
              "smtClean": null,
              "spc": null,
              "strike": null,
              "sz": "1800",
              "u": null,
              "ln": null,
              "solidFill": "tx1",
              "effectLst": null,
              "highlight": null,
              "uLn": null,
              "uFill": null,
              "latin": "+mn-lt",
              "ea": "+mn-ea",
              "cs": "+mn-cs",
              "sym": null,
              "hlinkClick": null,
              "hlinkMouseOver": null,
              "rtl": null,
              "extLst": null
            },
            "extLst": null,
            "buClr": "accent3",
            "buSzPts": null,
            "buFont": "Arial",
            "buChar": "•",
            "buNone": null
          },
          {
            "lvl": null,
            "algn": "l",
            "defTabSz": 914400,
            "eaLnBrk": true,
            "fontAlgn": null,
            "hangingPunct": "1",
            "indent": -228600,
            "latinLnBrk": false,
            "marL": 1280160,
            "marR": null,
            "rtl": false,
            "lnSpc": null,
            "spcBef": 20000,
            "spcAft": null,
            "tabLst": null,
            "defRPr": {
              "altLang": null,
              "b": null,
              "baseline": null,
              "bmk": null,
              "cap": null,
              "dirty": null,
              "err": null,
              "i": null,
              "kern": "1200",
              "kumimoji": null,
              "lang": "zh-TW",
              "noProof": null,
              "normalizeH": null,
              "smtClean": null,
              "spc": null,
              "strike": null,
              "sz": "1600",
              "u": null,
              "ln": null,
              "solidFill": "tx1",
              "effectLst": null,
              "highlight": null,
              "uLn": null,
              "uFill": null,
              "latin": "+mn-lt",
              "ea": "+mn-ea",
              "cs": "+mn-cs",
              "sym": null,
              "hlinkClick": null,
              "hlinkMouseOver": null,
              "rtl": null,
              "extLst": null
            },
            "extLst": null,
            "buClr": "accent4",
            "buSzPts": null,
            "buFont": "Arial",
            "buChar": "•",
            "buNone": null
          },
          {
            "lvl": null,
            "algn": "l",
            "defTabSz": 914400,
            "eaLnBrk": true,
            "fontAlgn": null,
            "hangingPunct": "1",
            "indent": -228600,
            "latinLnBrk": false,
            "marL": 1554480,
            "marR": null,
            "rtl": false,
            "lnSpc": null,
            "spcBef": 20000,
            "spcAft": null,
            "tabLst": null,
            "defRPr": {
              "altLang": null,
              "b": null,
              "baseline": "0",
              "bmk": null,
              "cap": null,
              "dirty": null,
              "err": null,
              "i": null,
              "kern": "1200",
              "kumimoji": null,
              "lang": "zh-TW",
              "noProof": null,
              "normalizeH": null,
              "smtClean": null,
              "spc": null,
              "strike": null,
              "sz": "1400",
              "u": null,
              "ln": null,
              "solidFill": "tx1",
              "effectLst": null,
              "highlight": null,
              "uLn": null,
              "uFill": null,
              "latin": "+mn-lt",
              "ea": "+mn-ea",
              "cs": "+mn-cs",
              "sym": null,
              "hlinkClick": null,
              "hlinkMouseOver": null,
              "rtl": null,
              "extLst": null
            },
            "extLst": null,
            "buClr": "accent5",
            "buSzPts": null,
            "buFont": "Arial",
            "buChar": "•",
            "buNone": null
          }
        ],
        "vert": "horz",
        "lIns": "91440",
        "tIns": "45720",
        "rIns": "91440",
        "bIns": "45720",
        "rtlCol": "0"
      },
      "name": "Text Placeholder 2"
    },
    {
      "id": "7",
      "type": "shape",
      "spType": null,
      "x": 8458200,
      "y": 0,
      "cx": 685800,
      "cy": 6858000,
      "presetGeom": "rect",
      "textBody": {
        "paragraphs": [
          {}
        ],
        "rtlCol": "0",
        "anchor": "ctr"
      },
      "name": "Rectangle 6"
    },
    {
      "id": "8",
      "type": "shape",
      "spType": null,
      "x": 8458200,
      "y": 5486400,
      "cx": 685800,
      "cy": 685800,
      "presetGeom": "rect",
      "textBody": {
        "paragraphs": [
          {}
        ],
        "rtlCol": "0",
        "anchor": "ctr"
      },
      "name": "Rectangle 7"
    },
    {
      "id": "6",
      "type": "shape",
      "spType": "sldNum",
      "x": 8531788,
      "y": 5648960,
      "cx": 548640,
      "cy": 396240,
      "presetGeom": "bracketPair",
      "textBody": {
        "paragraphs": [
          {}
        ],
        "vert": "horz",
        "lIns": "0",
        "tIns": "0",
        "rIns": "0",
        "bIns": "0",
        "rtlCol": "0",
        "anchor": "ctr"
      },
      "name": "Slide Number Placeholder 5"
    },
    {
      "id": "5",
      "type": "shape",
      "spType": "ftr",
      "x": 7586910,
      "y": 4048760,
      "cx": 2367281,
      "cy": 365760,
      "presetGeom": "rect",
      "textBody": {
        "paragraphs": [
          {}
        ],
        "vert": "horz",
        "lIns": "91440",
        "tIns": "45720",
        "rIns": "91440",
        "bIns": "45720",
        "rtlCol": "0",
        "anchor": "ctr"
      },
      "name": "Footer Placeholder 4"
    },
    {
      "id": "4",
      "type": "shape",
      "spType": "dt",
      "x": 7551351,
      "y": 1645920,
      "cx": 2438399,
      "cy": 365760,
      "presetGeom": "rect",
      "textBody": {
        "paragraphs": [
          {}
        ],
        "vert": "horz",
        "lIns": "91440",
        "tIns": "45720",
        "rIns": "91440",
        "bIns": "45720",
        "rtlCol": "0",
        "anchor": "ctr"
      },
      "name": "Date Placeholder 3"
    },
    null
  ]
}
```

還有一些問題，不過距離不太遠。





## 後續的調整

原先的剖析方式，是盡量讓資料欄位完整，沒資料就先填null，不過從結果看起來有些問題。因為資料的複製目前使用`Object.assign(parent, child)`，而null還是一個物件，所以一些值會被null蓋過去XD

先把null都改成undefined試試，還不能解決的話，就只好在資料有這些欄位時才建立這些欄位。