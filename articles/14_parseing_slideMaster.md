# 14 - 剖析母片資訊

母片資訊的重點，在於投影片的背景，主要是在母片中定義好的，所以在投影片的圖形資訊中，必須先把背景的圖形放進去





## 剖析的方式

母片中包含很多圖形，其中會被取代掉的其實是圖形範本（place holder），他只是一個為了編輯母片方便所使用的示意。在剖析的時候，就需要把這些捨棄掉，只留下真正會畫出的圖形。

place holder在他的p:nvPr屬性中會有一個p:ph屬性，像這樣：

```json
{
  "p:nvSpPr": [
    {
      "p:cNvPr": [
        {
          "$": {
            "id": "2",
            "name": "Title Placeholder 1"
          }
        }
      ],
      "p:cNvSpPr": [
        {
          "a:spLocks": [
            {
              "$": {
                "noGrp": "1"
              }
            }
          ]
        }
      ],
      "p:nvPr": [
        {
          "p:ph": [
            {
              "$": {
                "type": "title"
              }
            }
          ]
        }
      ]
    }
  ],
  ...
```

所以可以透過這個特性來排除。

parsing出來的圖形資訊，像這樣：

```json
shapes: [
    {
        id: '7',
        type: 'shape',
        name: 'Rectangle 6',
        x: 8458200,
        y: 0,
        cx: 685800,
        cy: 6858000,
        presetGeom: 'rect',
        solidFill: {
            srcRgbColor: 'tx2'
        }
    },
    {
        id: '8',
        type: 'shape',
        name: 'Rectangle 7',
        x: 8458200,
        y: 5486400,
        cx: 685800,
        cy: 685800,
        presetGeom: 'rect',
        solidFill: {
            srcRgbColor: 'accent1'
        }
    },
...
```

這兩個也是投影片中的背景使用的圖形資訊。最後測試一下：

![14-001.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/14-001.png)

通過測試沒問題。





## 一些問題

目前程式沒有做更詳細的判斷，其實有可能一些母片是沒背景的，或是背景是有圖片的等等，這些狀況還要加進去。不過這樣就需要加入更多的簡報檔作為測試資料...目前還是先用一個測試的簡報檔擋著，先讓功能可以做出來XD

