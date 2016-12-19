# 05 - 單位的換算

Office OpenXML裡面使用的「單位」，目前看到的有幾個：長度、指定百分比、指定字型大小等，就用03的例子來實驗改進一下



## 長度

ECMA-376使用一種叫做EMU(English Metric Unit, 英制公制單位)的長度單位來指定長度，包含座標位置、圖形的長寬等。EMU簡單的定義是：

> Throughout ECMA-376, the EMU is used as a unit of measurement for length. An EMU is defined as follows:
> $$
> 1 emu = 1/914400 inch = 1/360000 cm
> $$
> (from ECMA-376 Fifth Edition, P.2738)

為何會使用這樣的單位？主要是為了印刷的精度，所以盡量讓它能使用整數運算，並且可以在英制與公制之間快速換算。

在螢幕上顯示的話，通常應該是根據螢幕的解析度做換算：

```
1 pixel = 1 emu / 914400 * ppi(pixel per inch)
```

不過在我的需求上，會根據Canvas的大小做換算，這樣就不需要使用解析度的設定。在ppt/presentation.xml中有簡報大小的設定：

```xml
<p:sldSz cx="9144000" cy="6858000" type="screen4x3" />
```

這樣簡報就會設定為在4:3的螢幕上播放，寬度為9144000emu，高度為6858000emu。超過這個範圍的部分，在投影片中就看不到了。假設我們使用一個寬高比4:3的Canvas，寬度為800 pixels，高度為600 pixels，這樣要把emu換算成pixel，只要乘以```800/9144000```就可以。



## 百分比

百分比會在圖片裁切時使用，ECMA-376允許的格式可以用Regular Expression來表示：

```
-?[0-9]+(\.[0-9]+)?%
```

不過pptx裡面不是這樣XD，在嵌入圖片的部分，如果有做裁切，會像這樣指定：

```xml
<a:srcRect l="4445" t="32816" r="5222" b="33221" />
```

做過一些測試後，發現如果要換算成百分比，需要把這些數字除以1000，實際拿來跟數字換算的話就要除以100000。這跟ECMA-376不太一樣XD



## 字型大小

ECMA-376中，使用point*100（也就是pt*100）這樣的單位來指定字型大小，例如：

```xml
<a:rPr lang="zh-TW" altLang="en-GB" sz="2400">
```

這樣指定的字型大小是24pt，由於```1pt = 1/72 inch```，所以可以用EMU的公式換算成emu，然後再得到換算出的pixel：

```
result pixel = (2400 / 100 / 72 * 914400) emu * (800 / 9144000)
```

結果大約是27 pixels（實際上是26.66666666….無法整除XD）。



## 一些參考資料

1. ECMA-376 Fifth Edition, Part 1, "*20. DrawingML - Framework Reference

   Material*"

2. [English Metric Units and Open XML](http://polymathprogrammer.com/2009/10/22/english-metric-units-and-open-xml/)

3. [Why EMUs?](http://archive.oreilly.com/pub/post/what_is_an_emu.html)



## 修正後的例子

範例網址：[expample002.html](https://s3-ap-southeast-1.amazonaws.com/fillanoexamples/ironman2016/example002.html)

在Canvas繪製出的結果：

![05-011.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/05-011.png)



看起來跟PowerPoint中的投影片有一點像了：

![05-010.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/05-010.png)

嗯，字型還是有差，另外文字的垂直位置其實沒有做好，目前只是個work arround，等到後面再解決吧。

