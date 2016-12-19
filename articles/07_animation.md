# 07 - 動畫的基礎

在Canvas做動畫不難，其實只是重複兩個動作：

1. 清掉舊畫面
2. 繪製出目前的畫面

很久以前，在HTML中做動畫，大概都是透過setTimeout()或setInterval()，不過現在瀏覽器有提供更好的工具，可以提供更精確的控制，而且使用很簡單。





## 使用requestAnimationFrame()

呼叫requestAnimationFrame()時，它做的事情很簡單，就是在大約1/60秒後呼叫傳給它的callback函數，並且用當時的「高精度」timestamp當做參數傳給這個callback。

如果要讓動畫一格一格畫下去，必須在這個callback中重新呼叫一次，否則不會有下一步的動作。簡單的使用像這樣：

```javascript
function startAnimation() {
  var start = null;
  var limit = 2000;
  var count = 0;
  requestAnimationFrame(animation);
  function animation(t) {
    count++;
    if(start === null) start = t;
    if(t-start < limit) {
      console.log(t, start, count);
      requestAnimationFrame(animation);
    } else {
      console.log('done.');
    }
  }
}
```

這樣會在大約兩秒間執行完畢，並在console印出120次時間及次數的變化。這裡面的時間單位，整數部分的單位是千分之一秒，如果機器及環境許可，小數的部分可以精確至10微秒。

使用上可以參考MDN上的說明：[Window.requestAnimationFrame()](https://developer.mozilla.org/zh-TW/docs/Web/API/Window.requestAnimationFrame)





## 簡單的例子

接下來就寫一個簡單的例子，模仿簡報中的動畫效果：[example003.html](https://s3-ap-southeast-1.amazonaws.com/fillanoexamples/ironman2016/example003.html)

打開網頁後，按下「顯示簡報」按鈕，就會把畫面畫到Canvas中：

![07-001.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/07-001.png)

接下來按下「動畫一」按鈕，可以看到文字方塊右移的效果。下圖是移動後的結果：

![07-002.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/07-002.png)

同樣地，按下「動畫二」按鈕，可以看到文字方塊下移的效果。下圖是移動後的結果：

![07-003.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/07-003.png)

按下「reset」會重新載入網頁。沒有重新載入的話，只會顯示移動後的結果。





## 需要的改進

如果是在PowerPoint中，這個移動的過程會有加減速。這個部分沒有實作進去，可能在後續的例子繼續改進吧。

在文字的繪製上也有一些問題，雖然指定了用「新細明體」，但是在Chrome還是用Arial來繪製...這部份也許可以透過在CSS中透過@font-face載入web font的方式來解決，不過還需要再研究看看怎麼做比較合適（而且還會有版權問題XD）。

另外，如果去看程式，會發現我正在嘗試用手動的方式把pptx中的xml資料放到一些變數中，不過目前還沒有完整的想法。還需要更熟悉資料的定義以及嘗試在程式中實作看看，需要的是怎樣的結構。

過去在canvas繪圖效能還不那麼好的時代，像這樣做每次都要整個畫面重繪的動畫，可能會導致動畫播放的過程中畫面閃爍。如果發生這樣的狀況，常用的方法是double buffer，簡單說就是先把結果在一個看不到的canvas中繪出，然後再把這個canvas中的畫面一次更新到我們要看的canvas中。不過測試中沒看到這樣的狀況，所以就先不這樣做了。（不知道來不來得及實作轉場效果，如果要實作的話，改用這個方法會比較方便繪製畫面）

明天的話，先來嘗試處理簡報中的各個部件的關聯性，並且看看怎麼取出繪製圖形「Shape」，在xml中主要是定義在p:sp tag中的資訊，但是還會從佈景主題、母片、版面配置跟投影片中把資訊彙整成投影片繪製需要的資料。處理資料是最複雜的部份，就慢慢來吧。