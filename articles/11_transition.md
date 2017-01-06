# 11 - 試做轉場特效

之前的畫面繪製，都只有一個投影片，所以使用的資料都只有一個投影片。轉場特效通常會套用在投影片之間，這裡的試做就需要多張投影片，所以就需要調整一下了。





## 調整程式，逐步套用剖析出來的資料結構

大致上的調整方向，就是逐步套用昨天開始的剖析結果。因為很多東西還沒實作，所以其實還會調整XD

不過至少需要把幾個部分調整一下：

1. 繪製投影片：需要能繪製到不同的Canvas，因為轉場特效通常至少要有兩個Canvas才做得出來
2. 繪製圖形：目前暫時把圖形分成圖形以及圖片，分別會用不同方法繪製。文字方塊也是圖形，所以還是用圖形繪製的方法，只是偵測是否有文字需要繪製
3. 投影片資料：目前只擷取一部分格式，而投影片資料還沒做好，所以只是把之前的格式先稍微調整過，盡量不改太多程式，畢竟目前是要測試轉場特效

實際要套用的話，投影片資料應該改用rid當做key，在簡報資料中會有一個slideIdList，這才是投影片的實際順序。

圖形的部份更複雜，背景實際上定義在母片，投影片版面配置定義在版面配置，使用的圖形定義在投影片，而且各自會有自定資料蓋過前一個。除了這樣的關係，圖形還會有可選的「建構清單」，定義哪些在看到的時候就會出現，其他的會按照怎樣的方式出現等等。

不多說了，先來看看轉場特效...





## ECMA-376定義的轉場特效

查了一下規格書，ECMA-376定義了21種轉場特效：

1. checker
2. blinds
3. circle
4. comb
5. cover
6. cut
7. diamond
8. dissolve
9. fade
10. newsflash
11. plus
12. pull
13. push
14. random
15. randomBar
16. split
17. strips
18. wedge
19. wheel
20. wipe
21. zoom

定義的方式主要是用一個黑白的Pattern，配合動態效果的說明，來簡單說明怎麼呈現。例如comb：

```xml
<p:transition>
    <p:comb dir="horz"/>
</p:transition>
```

呈現方式的示意：

![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/11-001.png)

實際上額外使用兩個Canvas，就足以應付所有的轉場效果，來看看怎麼做。





## 轉場的實作方式

1. 使用一個Canvas，繪製下一張投影片
2. 使用另外一個Canvas做遮罩，用黑白雙色，隨著時間變化繪製上面的Pattern
3. 每次次變化遮罩上的圖案，就根據遮罩上每個圖素是黑是白，來決定是否要把下一張投影片相同位置的圖素寫到投影片上
4. 當遮罩全黑，就完整取代了前一張投影片了，轉場結束

其實並不難。在Canvas 2D Context的部份，只要透過`getImageData()`方法取得ImageData，從裡面取出像素資料做判斷並寫入目標的ImageData，最後透過`putImageData()` 方法將像素資料寫回就可以。

做像素處理對效能會有比較大的衝擊，在Chrome上跑還蠻順暢，到了Firefox就...





## 成果

做好的範例：[example005.html](https://s3-ap-southeast-1.amazonaws.com/fillanoexamples/ironman2016/example005.html)

裡面有兩個投影片，第一張沒有轉場效果，第二張有。一載入就會看到第一張投影片：

![11-002.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/11-002.png)

按下「簡報二」，就會出現兩秒的轉場效果：

![11-003.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/11-003.png)

載入完成：

![11-004.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/11-004.png)

大致上就是這樣囉。

明天來繼續處理資料剖析的部份...不過最近手痛，可能進度得放緩一點XD





