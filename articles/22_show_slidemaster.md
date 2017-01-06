# 22 - 用剖析出的資料試畫母片

大致上剖析出母片資料，除了寫測試之外，還是先試試看可不可以正常畫出來XD





## 目前產出的母片資料

先看看目前產出的母片資料長怎樣：

```json
  "background": "bg2",
  "shapes": [
    {
      "id": 2,
      "name": "Title Placeholder 1",
      "spType": "title",
      "x": 457200,
      "y": 274638,
      "cx": 7620000,
      "cy": 1143000,
      "presetGeom": "rect",
      "textBody": {
        "paragraphs": [
          {
            "runs": [
              {
                "type": "r",
                "lang": "zh-TW",
                "altLang": "en-US",
                "smtClean": 0,
                "text": "按一下以編輯母片標題樣式"
              },
              {
                "type": "end"
              }
            ]
          }
        ],
        "vert": "horz",
        "lIns": 91440,
        "tIns": 45720,
        "rIns": 91440,
        "bIns": 45720,
        "rtlCol": 0,
        "anchor": "ctr"
      }
    },
    {
      "id": 3,
      "name": "Text Placeholder 2",
      "spType": "body",
      "x": 457200,
      "y": 1600200,
      "cx": 7620000,
      "cy": 4800600,
      "presetGeom": "rect",
      "textBody": {
        "paragraphs": [
          {
            "lvl": 0,
            "defRPr": {
              "lang": "zh-TW",
              "sz": 2200,
              "kern": 1200,
              "solidFill": "tx1",
              "latin": "+mn-lt",
              "ea": "+mn-ea",
              "cs": "+mn-cs"
            },
            "marL": 342900,
            "indent": -228600,
            "algn": "l",
            "defTabSz": 914400,
            "rtl": false,
            "eaLnBrk": true,
            "latinLnBrk": false,
            "hangingPunct": 1,
            "spcBef": 20000,
            "buClr": "accent1",
            "buFont": "Arial",
            "buChar": "•",
            "runs": [
              {
                "type": "r",
                "lang": "zh-TW",
                "sz": 2200,
                "kern": 1200,
                "solidFill": "tx1",
                "latin": "+mn-lt",
                "ea": "+mn-ea",
                "cs": "+mn-cs",
                "altLang": "en-US",
                "smtClean": 0,
                "text": "按一下以編輯母片文字樣式"
              }
            ]
          },
          {
            "lvl": 1,
            "defRPr": {
              "lang": "zh-TW",
              "sz": 2000,
              "kern": 1200,
              "solidFill": "tx1",
              "latin": "+mn-lt",
              "ea": "+mn-ea",
              "cs": "+mn-cs"
            },
            "marL": 640080,
            "indent": -228600,
            "algn": "l",
            "defTabSz": 914400,
            "rtl": false,
            "eaLnBrk": true,
            "latinLnBrk": false,
            "hangingPunct": 1,
            "spcBef": 20000,
            "buClr": "accent2",
            "buFont": "Arial",
            "buChar": "•",
            "runs": [
              {
                "type": "r",
                "lang": "zh-TW",
                "sz": 2000,
                "kern": 1200,
                "solidFill": "tx1",
                "latin": "+mn-lt",
                "ea": "+mn-ea",
                "cs": "+mn-cs",
                "altLang": "en-US",
                "smtClean": 0,
                "text": "第二層"
              }
            ]
          },
          {
            "lvl": 2,
            "defRPr": {
              "lang": "zh-TW",
              "sz": 1800,
              "kern": 1200,
              "solidFill": "tx1",
              "latin": "+mn-lt",
              "ea": "+mn-ea",
              "cs": "+mn-cs"
            },
            "marL": 1005840,
            "indent": -228600,
            "algn": "l",
            "defTabSz": 914400,
            "rtl": false,
            "eaLnBrk": true,
            "latinLnBrk": false,
            "hangingPunct": 1,
            "spcBef": 20000,
            "buClr": "accent3",
            "buFont": "Arial",
            "buChar": "•",
            "runs": [
              {
                "type": "r",
                "lang": "zh-TW",
                "sz": 1800,
                "kern": 1200,
                "solidFill": "tx1",
                "latin": "+mn-lt",
                "ea": "+mn-ea",
                "cs": "+mn-cs",
                "altLang": "en-US",
                "smtClean": 0,
                "text": "第三層"
              }
            ]
          },
          {
            "lvl": 3,
            "defRPr": {
              "lang": "zh-TW",
              "sz": 1600,
              "kern": 1200,
              "solidFill": "tx1",
              "latin": "+mn-lt",
              "ea": "+mn-ea",
              "cs": "+mn-cs"
            },
            "marL": 1280160,
            "indent": -228600,
            "algn": "l",
            "defTabSz": 914400,
            "rtl": false,
            "eaLnBrk": true,
            "latinLnBrk": false,
            "hangingPunct": 1,
            "spcBef": 20000,
            "buClr": "accent4",
            "buFont": "Arial",
            "buChar": "•",
            "runs": [
              {
                "type": "r",
                "lang": "zh-TW",
                "sz": 1600,
                "kern": 1200,
                "solidFill": "tx1",
                "latin": "+mn-lt",
                "ea": "+mn-ea",
                "cs": "+mn-cs",
                "altLang": "en-US",
                "smtClean": 0,
                "text": "第四層"
              }
            ]
          },
          {
            "lvl": 4,
            "defRPr": {
              "lang": "zh-TW",
              "sz": 1400,
              "kern": 1200,
              "baseline": 0,
              "solidFill": "tx1",
              "latin": "+mn-lt",
              "ea": "+mn-ea",
              "cs": "+mn-cs"
            },
            "marL": 1554480,
            "indent": -228600,
            "algn": "l",
            "defTabSz": 914400,
            "rtl": false,
            "eaLnBrk": true,
            "latinLnBrk": false,
            "hangingPunct": 1,
            "spcBef": 20000,
            "buClr": "accent5",
            "buFont": "Arial",
            "buChar": "•",
            "runs": [
              {
                "type": "r",
                "lang": "zh-TW",
                "sz": 1400,
                "kern": 1200,
                "baseline": 0,
                "solidFill": "tx1",
                "latin": "+mn-lt",
                "ea": "+mn-ea",
                "cs": "+mn-cs",
                "altLang": "en-US",
                "smtClean": 0,
                "text": "第五層"
              },
              {
                "type": "end"
              }
            ]
          }
        ],
        "vert": "horz",
        "lIns": 91440,
        "tIns": 45720,
        "rIns": 91440,
        "bIns": 45720,
        "rtlCol": 0
      }
    },
    {
      "id": 7,
      "name": "Rectangle 6",
      "x": 8458200,
      "y": 0,
      "cx": 685800,
      "cy": 6858000,
      "presetGeom": "rect",
      "textBody": {
        "paragraphs": [
          {
            "lvl": 0,
            "defRPr": {
              "lang": "en-US",
              "sz": 1800,
              "kern": 1200,
              "solidFill": "tx1",
              "latin": "+mn-lt",
              "ea": "+mn-ea",
              "cs": "+mn-cs"
            },
            "marL": 0,
            "algn": "l",
            "defTabSz": 914400,
            "rtl": false,
            "eaLnBrk": true,
            "latinLnBrk": false,
            "hangingPunct": 1,
            "runs": [
              {
                "type": "end"
              }
            ]
          }
        ],
        "rtlCol": 0,
        "anchor": "ctr"
      }
    },
    {
      "id": 8,
      "name": "Rectangle 7",
      "x": 8458200,
      "y": 5486400,
      "cx": 685800,
      "cy": 685800,
      "presetGeom": "rect",
      "textBody": {
        "paragraphs": [
          {
            "lvl": 0,
            "defRPr": {
              "lang": "en-US",
              "sz": 1800,
              "kern": 1200,
              "solidFill": "tx1",
              "latin": "+mn-lt",
              "ea": "+mn-ea",
              "cs": "+mn-cs"
            },
            "marL": 0,
            "algn": "l",
            "defTabSz": 914400,
            "rtl": false,
            "eaLnBrk": true,
            "latinLnBrk": false,
            "hangingPunct": 1,
            "runs": [
              {
                "type": "end"
              }
            ]
          }
        ],
        "rtlCol": 0,
        "anchor": "ctr"
      }
    },
    {
      "id": 6,
      "name": "Slide Number Placeholder 5",
      "spType": "sldNum",
      "x": 8531788,
      "y": 5648960,
      "cx": 548640,
      "cy": 396240,
      "presetGeom": "bracketPair",
      "textBody": {
        "paragraphs": [
          {
            "runs": [
              {
                "type": "fld"
              },
              {
                "type": "end"
              }
            ]
          }
        ],
        "vert": "horz",
        "lIns": 0,
        "tIns": 0,
        "rIns": 0,
        "bIns": 0,
        "rtlCol": 0,
        "anchor": "ctr"
      }
    },
    {
      "id": 5,
      "name": "Footer Placeholder 4",
      "spType": "ftr",
      "x": 7586910,
      "y": 4048760,
      "cx": 2367281,
      "cy": 365760,
      "presetGeom": "rect",
      "textBody": {
        "paragraphs": [
          {
            "runs": [
              {
                "type": "end"
              }
            ]
          }
        ],
        "vert": "horz",
        "lIns": 91440,
        "tIns": 45720,
        "rIns": 91440,
        "bIns": 45720,
        "rtlCol": 0,
        "anchor": "ctr"
      }
    },
    {
      "id": 4,
      "name": "Date Placeholder 3",
      "spType": "dt",
      "x": 7551351,
      "y": 1645920,
      "cx": 2438399,
      "cy": 365760,
      "presetGeom": "rect",
      "textBody": {
        "paragraphs": [
          {
            "runs": [
              {
                "type": "fld"
              },
              {
                "type": "end"
              }
            ]
          }
        ],
        "vert": "horz",
        "lIns": 91440,
        "tIns": 45720,
        "rIns": 91440,
        "bIns": 45720,
        "rtlCol": 0,
        "anchor": "ctr"
      }
    },
    {
      "type": "pic",
      "id": 9,
      "name": "圖片 8",
      "descr": "image6.png",
      "rotWithShape": true,
      "blip": {
        "embed": "ppt/media/image2.png",
        "srcRect": {
          "l": 5681,
          "t": 34663,
          "r": 6586,
          "b": 32076
        }
      },
      "x": 6145327,
      "y": 6200380,
      "cx": 2312871,
      "cy": 657618,
      "presetGeom": "rect"
    }
  ]
```

乍看之下，好像東西都有進去...然後把它塞進之前做的範例跑跑看。



![22-001.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/22-001.png)

好像怪怪的XD，跟powerpoint對照一下看看：

![22-002.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/22-002.png)

圖形的框線是我另外加的，以便確認文字的位置。日期、頁尾及頁碼目前還沒有支援就算了。來歸納一下問題...





## 需要進一步調整的地方

1. 圖形的文字本體（txBody）的背景色沒有正確抓出來
2. 標題的樣式資訊差很多，完全消失了
3. 列表符號（bullet）的大小沒抓到，我是寫死成22pt，所以對齊的很奇怪
4. 圖形及文字方向的支援（本來想暫時不做）
5. 頁面的程式要進一步調整



總之，還有一大段距離，至少母片資料正確才能繼續下去做版面配置及投影片阿，裡面有許多剖析的功能是重複的XD



母片繪製的範例，可以到下列網址打開：[example007.html](https://s3-ap-southeast-1.amazonaws.com/fillanoexamples/ironman2016/example007.html)

