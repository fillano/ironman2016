# 09 - 動畫改進

之前做的動畫效果很簡單，只有右移跟下移的直線動作。另一個基本的動畫是依照弧線來移動，就試著把它實作出來。

jQuery的動畫效果，預設會有加減速，而在PowerPoint的動畫中也會有，雖然作法其實不一樣，但是先來試試jQuery的實作。

調整過的試做：[example004.html](https://s3-ap-southeast-1.amazonaws.com/fillanoexamples/ironman2016/example004.html)



## 加入弧線移動動畫

在PowerPoint加入弧線移動的動畫時，在移動的資料中會有像這樣的資料：

```xml
<p:animMotion path="M 0 0  L 0.067 0.05331  C 0.081 0.06531  0.102 0.07198  0.124 0.07198  C 0.149 0.07198  0.169 0.06531  0.183 0.05331  L 0.25 0  E">
</p:animMotion>
```

path是定義移動路徑，語法是由字母加上數個數字參數構成。其中：

1. M：是Move To的簡寫，後面接一組座標
2. E：是End
3. L：是Line To的簡寫，後面接一組座標，表示從目前座標畫一條直線到指定的座標
4. C：是Curve To的簡寫，後面接三組座標，表示二次貝茲曲線的三個座標參數

不過...一開始就做這些有點複雜，而且他給的座標其實沒連續XD，還要自己插進Line To的動作接起來，所以我先簡化成一個C來操作：

```xml
<p:animMOtion path="M 0 0  C 0 0 0.125 0.125 0.25 0  E">
</p:animMOtion>
```

至於要怎麼移動...可以參考維基百科的說明：[貝茲曲線](https://zh.wikipedia.org/wiki/%E8%B2%9D%E8%8C%B2%E6%9B%B2%E7%B7%9A)

裡面有定義二次貝茲曲線的公式（圖片來自維基百科）：

![](https://wikimedia.org/api/rest_v1/media/math/render/svg/d28ed03475106564d4a959647acb2b7fa34935ef)

一個簡單的實作就是：

```javascript
function define_quadratic_bezier(x0, y0, x1, y1, x2, y2) {
    return function (t) {
        if (t < 0 || t > 1) throw "[define_quadratic_bezier] t must between 0 and 1.";
        var px = Math.pow(1-t, 2) * x0 + 2 * t * (1-t) * x1 + Math.pow(t, 2) * x2;
        var py = Math.pow(1-t, 2) * y0 + 2 * t * (1-t) * y1 + Math.pow(t, 2) * y2;
        return [px, py];
    };
}
```

使用上先呼叫`var b = define_quadratic_bezier(...)`定義好曲線，然後以`t`作為參數呼叫`b(t)`，就可以算出在t這個位置的x, y座標。

在繪製動畫時，這個t就是繪製進度，例如進度到百分之四十五時，t就是0.45。

Canvas支援二次及三次貝茲曲線的繪製，不過我們無法傳t給他取得t時的座標...所以這一段只能自己做了。





## 加入加減速效果

jQuery的動畫，有一個參數叫做easing，可以調整移動的速度。預設的效果是swing，透過它來調整t，就能做出加減速的效果。

這個函數很簡單：

```javascript
function easing(p) {
    return 0.5 - Math.cos(p * Math.PI) / 2;
}
```

傳入一個介於0與1之間的參數，會看到越遠離0.5的時候，t的變化會變小，所以套用時，隨著動畫進度，就會有加減速的效果。





## 初步處理簡報縮放

除了這些部分，也稍微嘗試了一下在頁面載入時，自動根據頁面大小來縮放簡報，這樣簡報呈現時會自動滿版。之前已經處理過簡報大小的座標轉換，所以並不會花太多時間。

另外一個問題是全螢幕支援，就放到後面慢慢做。這次鐵人賽我有看到有人介紹這個：[screenfull.js](https://github.com/sindresorhus/screenfull.js/)，可以更方便地做全螢幕操作。不過我的需求是在進入全螢幕時觸發事件，然後根據畫面來調整簡報大小，問題是目前瀏覽器對於進入、離開全螢幕事件的支援還很差...所以可能要考慮用古老的setInterval來解決XD





## 其他問題

其實還有問題需要額外調整，就是動畫的方向，目前只有左到右，上到下的方向會正確跑XD，不過有點懶，之後再調整...



