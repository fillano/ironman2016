# 23 - 剖析佈景主題及版面配置

昨天畫出母片資料後，可以看到一些需要改善的部份，先來調整一下。



## 調整母片資料

這部份主要調整了幾個部分：

1. 不需要繪製圖形背景時，不要繪製
2. 剖析邏輯修正，這是導致標題沒出來的原因XD
3. 顏色架構，直接給RGB的Hex

修正後的畫面比較正確：

![23-001.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/23-001.png)



## 剖析佈景主題

差一點忘記剖析佈景主題，是為了要直接透過顏色定義及對應來給值的時候，才發現...

還好這部份目前需要的資料不太多，除了顏色定義，另外就是字型的定義。不過佈景主題中，東亞的字型並未指定，而底下local設定中，是`Hant`對應到`新細明體`，但是語言的資料是給`zh-TW`，還在找怎樣抓住正確的字型對應比較好。不過不管問題，反正先把資料抓出來。

剖析出來的佈景主題資料目前長這樣：

```json
{
  "ppt/theme/theme1.xml": {
    "colorScheme": {
      "name": "相鄰",
      "dk1": "#2F2B20",
      "lt1": "#FFFFFF",
      "dk2": "#675E47",
      "lt2": "#DFDCB7",
      "accent1": "#A9A57C",
      "accent2": "#9CBEBD",
      "accent3": "#D2CB6C",
      "accent4": "#95A39D",
      "accent5": "#C89F5D",
      "accent6": "#B1A089",
      "hlink": "#D25814",
      "folHlink": "#849A0A"
    },
    "fontScheme": {
      "name": "相鄰",
      "majorFont": {
        "fonts": {
          "Jpan": "ＭＳ ゴシック",
          "Hang": "맑은 고딕",
          "Hans": "宋体",
          "Hant": "新細明體",
          "Arab": "Times New Roman",
          "Hebr": "Times New Roman",
          "Thai": "Angsana New",
          "Ethi": "Nyala",
          "Beng": "Vrinda",
          "Gujr": "Shruti",
          "Khmr": "MoolBoran",
          "Knda": "Tunga",
          "Guru": "Raavi",
          "Cans": "Euphemia",
          "Cher": "Plantagenet Cherokee",
          "Yiii": "Microsoft Yi Baiti",
          "Tibt": "Microsoft Himalaya",
          "Thaa": "MV Boli",
          "Deva": "Mangal",
          "Telu": "Gautami",
          "Taml": "Latha",
          "Syrc": "Estrangelo Edessa",
          "Orya": "Kalinga",
          "Mlym": "Kartika",
          "Laoo": "DokChampa",
          "Sinh": "Iskoola Pota",
          "Mong": "Mongolian Baiti",
          "Viet": "Times New Roman",
          "Uigh": "Microsoft Uighur",
          "Geor": "Sylfaen"
        },
        "latin": "Cambria",
        "ea": "",
        "cs": ""
      },
      "minorFont": {
        "fonts": {
          "Jpan": "ＭＳ 明朝",
          "Hang": "맑은 고딕",
          "Hans": "宋体",
          "Hant": "新細明體",
          "Arab": "Arial",
          "Hebr": "Arial",
          "Thai": "Cordia New",
          "Ethi": "Nyala",
          "Beng": "Vrinda",
          "Gujr": "Shruti",
          "Khmr": "DaunPenh",
          "Knda": "Tunga",
          "Guru": "Raavi",
          "Cans": "Euphemia",
          "Cher": "Plantagenet Cherokee",
          "Yiii": "Microsoft Yi Baiti",
          "Tibt": "Microsoft Himalaya",
          "Thaa": "MV Boli",
          "Deva": "Mangal",
          "Telu": "Gautami",
          "Taml": "Latha",
          "Syrc": "Estrangelo Edessa",
          "Orya": "Kalinga",
          "Mlym": "Kartika",
          "Laoo": "DokChampa",
          "Sinh": "Iskoola Pota",
          "Mong": "Mongolian Baiti",
          "Viet": "Arial",
          "Uigh": "Microsoft Uighur",
          "Geor": "Sylfaen"
        },
        "latin": "Calibri",
        "ea": "",
        "cs": ""
      }
    }
  }
}
```

問題如上所述XD





## 剖析版面配置

母片整理差不多一後，就開始進行版面配置。做法跟母片很類似，一些地方也只是呼叫同樣的函數。

抓出來的資料大概像這樣：

```json
"background": "#DFDCB7",
"shapes": [
    {
        "type": "shape",
        "id": 2,
        "name": "Title 1",
        "spType": "title",
        "x": 457200,
        "y": 274638,
        "cx": 7620000,
        "cy": 1143000,
        "presetGeom": "rect",
        "textBody": {
            "paragraphs": [
                {
                    "lvl": 0,
                    "defRPr": {
                        "lang": "zh-TW",
                        "sz": 4600,
                        "kern": 1200,
                        "cap": "none",
                        "spc": -100,
                        "baseline": 0,
                        "solidFill": "#675E47",
                        "latin": "+mj-lt",
                        "ea": "+mj-ea",
                        "cs": "+mj-cs"
                    },
                    "algn": "l",
                    "defTabSz": 914400,
                    "rtl": false,
                    "eaLnBrk": true,
                    "latinLnBrk": false,
                    "hangingPunct": 1,
                    "spcBef": 0,
                    "buNone": true,
                    "runs": [
                        {
                            "type": "r",
                            "lang": "zh-TW",
                            "sz": 4600,
                            "kern": 1200,
                            "cap": "none",
                            "spc": -100,
                            "baseline": 0,
                            "solidFill": "#675E47",
                            "latin": "+mj-lt",
                            "ea": "+mj-ea",
                            "cs": "+mj-cs",
                            "altLang": "en-US",
                            "smtClean": 0,
                            "text": "按一下以編輯母片標題樣式"
                        },
                        {
                            "type": "end"
                        }
                    ]
                }
            ]
        }
    },
    {
        "type": "shape",
        "id": 3,
        "name": "Content Placeholder 2",
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
                        "lang": "en-US",
                        "sz": 1800,
                        "kern": 1200,
                        "solidFill": "#2F2B20",
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
                            "type": "r",
                            "lang": "zh-TW",
                            "sz": 1800,
                            "kern": 1200,
                            "solidFill": "#2F2B20",
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
                        "lang": "en-US",
                        "sz": 1800,
                        "kern": 1200,
                        "solidFill": "#2F2B20",
                        "latin": "+mn-lt",
                        "ea": "+mn-ea",
                        "cs": "+mn-cs"
                    },
                    "marL": 457200,
                    "algn": "l",
                    "defTabSz": 914400,
                    "rtl": false,
                    "eaLnBrk": true,
                    "latinLnBrk": false,
                    "hangingPunct": 1,
                    "runs": [
                        {
                            "type": "r",
                            "lang": "zh-TW",
                            "sz": 1800,
                            "kern": 1200,
                            "solidFill": "#2F2B20",
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
                        "lang": "en-US",
                        "sz": 1800,
                        "kern": 1200,
                        "solidFill": "#2F2B20",
                        "latin": "+mn-lt",
                        "ea": "+mn-ea",
                        "cs": "+mn-cs"
                    },
                    "marL": 914400,
                    "algn": "l",
                    "defTabSz": 914400,
                    "rtl": false,
                    "eaLnBrk": true,
                    "latinLnBrk": false,
                    "hangingPunct": 1,
                    "runs": [
                        {
                            "type": "r",
                            "lang": "zh-TW",
                            "sz": 1800,
                            "kern": 1200,
                            "solidFill": "#2F2B20",
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
                        "lang": "en-US",
                        "sz": 1800,
                        "kern": 1200,
                        "solidFill": "#2F2B20",
                        "latin": "+mn-lt",
                        "ea": "+mn-ea",
                        "cs": "+mn-cs"
                    },
                    "marL": 1371600,
                    "algn": "l",
                    "defTabSz": 914400,
                    "rtl": false,
                    "eaLnBrk": true,
                    "latinLnBrk": false,
                    "hangingPunct": 1,
                    "runs": [
                        {
                            "type": "r",
                            "lang": "zh-TW",
                            "sz": 1800,
                            "kern": 1200,
                            "solidFill": "#2F2B20",
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
                        "lang": "en-US",
                        "sz": 1800,
                        "kern": 1200,
                        "solidFill": "#2F2B20",
                        "latin": "+mn-lt",
                        "ea": "+mn-ea",
                        "cs": "+mn-cs"
                    },
                    "marL": 1828800,
                    "algn": "l",
                    "defTabSz": 914400,
                    "rtl": false,
                    "eaLnBrk": true,
                    "latinLnBrk": false,
                    "hangingPunct": 1,
                    "runs": [
                        {
                            "type": "r",
                            "lang": "zh-TW",
                            "sz": 1800,
                            "kern": 1200,
                            "solidFill": "#2F2B20",
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
            ]
        }
    },
    {
        "type": "shape",
        "id": 4,
        "name": "Date Placeholder 3",
        "spType": "dt",
        "rot": 16200000,
        "x": 7551351,
        "y": 1645920,
        "cx": 2438399,
        "cy": 365760,
        "presetGeom": "rect",
        "textBody": {
            "paragraphs": [
                {
                    "lvl": 0,
                    "defRPr": {
                        "lang": "en-US",
                        "sz": 1800,
                        "kern": 1200,
                        "solidFill": "#2F2B20",
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
                            "type": "fld"
                        },
                        {
                            "type": "end"
                        }
                    ]
                }
            ]
        }
    },
    {
        "type": "shape",
        "id": 5,
        "name": "Footer Placeholder 4",
        "spType": "ftr",
        "rot": 16200000,
        "x": 7586910,
        "y": 4048760,
        "cx": 2367281,
        "cy": 365760,
        "presetGeom": "rect",
        "textBody": {
            "paragraphs": [
                {
                    "lvl": 0,
                    "defRPr": {
                        "lang": "en-US",
                        "sz": 1800,
                        "kern": 1200,
                        "solidFill": "#2F2B20",
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
            ]
        }
    },
    {
        "type": "shape",
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
                    "lvl": 0,
                    "defRPr": {
                        "lang": "en-US",
                        "sz": 1800,
                        "kern": 1200,
                        "solidFill": "#2F2B20",
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
                            "type": "fld"
                        },
                        {
                            "type": "end"
                        }
                    ]
                }
            ]
        }
    }
]
```

（版面配置有十幾個檔，這裡只秀出一個）





## 試著畫出版面配置

昨天做好的例子，改一下應該就可以直接套用，來試試看...

![23-002.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/23-002.png)

有點怪，字都沒出來XD....明天再來檢查一下，問題出在哪裡。有可能我目前的一些處理資料的邏輯還有問題。

另外，有興趣的話，連結在此：[example008.html](https://s3-ap-southeast-1.amazonaws.com/fillanoexamples/ironman2016/example008.html)

（昨天母片的頁面也有更新，就長得跟第一張圖片一樣。）





