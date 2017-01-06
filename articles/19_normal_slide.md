# 19 - 畫出標準投影片

之前只顧著做各種文字方塊、動畫效果及轉場效果，反而沒有嘗試畫出一個標準投影片，也就是標題加上文字列表，雖然投影片資料剖析的部份還沒做好，不過還是用預計會產生的資料格式來畫畫看。





## 資料格式

使用到的JSON，在圖形的部份會長這樣：

```json
{
    id: '3',
    type: 'shape',
    name: '',
    x: 457200,
    y: 1600200,
    cx: 7620000,
    cy: 4800600,
    presetGeom: 'rect',
    textBody: {
        align: 'l',
        lIns: 91440,
        tIns: 45720,
        rIns: 91440,
        bIns: 45720,
        paragraphs: [
            {
                align: 'l',
                defTabSz: 914400,
                rtl: 0,
                eaLnBrk: 1,
                latinLnBrk: 0,
                hangingPunct: 1,
                spaceBefore: 0,
                buFont: 'Arial',
                buSize: 2200,
                buColor: 'accent1',
                buChar: '•',
                runs: [
                    {
                        lang: 'zh-TW',
                        size: 2200,
                        kern: 1200,
                        cap: 'none',
                        spc: -100,
                        face: '新細明體',
                        text: '第一條文字',
                        baseline: 0,
                        solidFill: {
                            srgbColor: 'tx2'
                        }
                    }
                ]
            },
            {
                align: 'l',
                defTabSz: 914400,
                rtl: 0,
                eaLnBrk: 1,
                latinLnBrk: 0,
                hangingPunct: 1,
                spaceBefore: 0,
                buFont: 'Arial',
                buSize: 2200,
                buColor: 'accent1',
                buChar: '•',
                runs: [
                    {
                        lang: 'zh-TW',
                        size: 2200,
                        kern: 1200,
                        cap: 'none',
                        spc: -100,
                        face: '新細明體',
                        text: '第二條文字',
                        baseline: 0,
                        solidFill: {
                            srgbColor: 'tx2'
                        }
                    }
                ]
            },
            {
                align: 'l',
                defTabSz: 914400,
                rtl: 0,
                eaLnBrk: 1,
                latinLnBrk: 0,
                hangingPunct: 1,
                spaceBefore: 0,
                buFont: 'Arial',
                buSize: 2200,
                buColor: 'accent1',
                buChar: '•',
                runs: [
                    {
                        lang: 'zh-TW',
                        size: 2200,
                        kern: 1200,
                        cap: 'none',
                        spc: -100,
                        face: '新細明體',
                        text: '第三條文字',
                        baseline: 0,
                        solidFill: {
                            srgbColor: 'tx2'
                        }
                    }
                ]
            },
            {
                align: 'l',
                defTabSz: 914400,
                rtl: 0,
                eaLnBrk: 1,
                latinLnBrk: 0,
                hangingPunct: 1,
                spaceBefore: 0,
                buFont: 'Arial',
                buSize: 2200,
                buColor: 'accent1',
                buChar: '•',
                runs: [
                    {
                        lang: 'zh-TW',
                        size: 2200,
                        kern: 1200,
                        cap: 'none',
                        spc: -100,
                        face: '新細明體',
                        text: '第四條文字',
                        baseline: 0,
                        solidFill: {
                            srgbColor: 'tx2'
                        }
                    }
                ]
            }
        ]
    }
}
```

由於剖析的部份還沒好，所以這些是手動彙整的。但是意思差不多。

跟一般的文字方塊比較起來，最大的差別是他會指定列表符號。內建的列表符號就是「•」，資料中還會指定它的字型、大小顏色等資料。

先來看一下畫出來的樣子：

![19-001.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/19-001.png)

另外，手動加了文字，調整顏色、字型大小（22->24）、粗體、斜體等：

![19-002.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/19-002.png)

大致上就這樣。





## 未完成的部份

其實有些東西沒做完：

1. 自動折行
2. 換行
3. 行高計算的方法
4. 垂直對齊的作法

等等。不過這些基本上是座標的微調，額外加上計算大致上就可以。

有興趣的話，可以看一看：[example006.html](https://s3-ap-southeast-1.amazonaws.com/fillanoexamples/ironman2016/example006.html)



