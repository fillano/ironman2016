# 03 - Canvas 2D Context繪圖的基礎：圖片、圖形與文字繪製

今天休息一下，簡單介紹Canvas 2D Context的基本繪圖功能。



## 取得Context

透過Canvas元素的getContext()方法，就可以取得2D Context，然後利用他來繪圖。像這樣：

```javascript
var ctx = document.getElementById('canvas').getContext('2d');
```



## 繪製圖片

繪製圖片需要先取得img元素，然後透過2D Context的drawImage()方法把圖片繪製到Canvas中。像這樣：

```javascript
var image1 = new Image();
image1.src = 'media/image1.jpeg';
ctx.drawImage(image1, 0, 0, 800, 600);
```



## 繪製圖形

2D Canvas有內建的圖形繪製方法，例如透過strokeRect()可以繪製空心的長方形，fillRect()可以繪製實心的長方形等。

不過更強悍的是他的路徑相關方法，利用他可以繪製各種不同的圖形：

1. moveTo()：移動到Canvas中的某個位置
2. beginPath()：開始路徑繪製
3. lineTo()：從目前的位置繪製直線路徑到指定的位置
4. quadraticCurveTo()：從目前的位置繪製二次曲線路徑到指定的位置（例如橢圓）
5. bezierCurveTo()：從目前的位置繪製貝茲曲線路徑到指定位置
6. arcTo()：從目前的位置繪製弧線到指定的位置
7. closePath()：結束路徑繪製
8. fill()：在路徑中填色。顏色可透過2D Context的fillStyle屬性來指定
9. stroke()：描繪出路徑。顏色可透過2D Context的fillStyle屬性來指定。透過lineWidth屬性可以指定線寬，透過lineCap屬性可以指定線段開始那一點的呈現方式，透過lineJoin屬性可以指定線段與線段接合的樣式。

通常透過簡單的三角函數計算下一個點的位置，然後用lineTo()就可以做出各種多邊形。例如正三角形：

```javascript
ctx.fillStyle = '#6699CC';
ctx.moveTo(400, 420);
ctx.beginPath();
ctx.lineTo(430, 420);
ctx.lineTo(430 - 30 * Math.cos(Math.PI/3), 420 - 30 * Math.sin(Math.PI/3));
ctx.lineTo(400, 420);
ctx.closePath();
ctx.fill();
```

這樣就可以以座標(400, 420)為正三角形的左下角，繪製邊長為30的正三角形。



## 繪製文字

利用2D Context繪製文字，只要簡單地透過fillText()或strokeText()方法就可以做到。用fillText()可以繪製出一般文字，用strokeText則是繪製出外框文字。

透過fillStyle屬性或是strokeStyle屬性就可以指定繪製的顏色，透過font屬性則可指定字型及風格。在排版的功能上，可以透過textAlign屬性指定文字對齊的方式，透過textBaseline則可以設定垂直對齊。透過measureText()方法，還可以計算出字串的寬度，對於非等寬的字型，可以用這個方式來計算是否需要折行。

來簡單繪製出文字看看：

```javascript
ctx.strokeStyle = '#114477';
ctx.textBaseline = 'bottom';
ctx.font = '900 36px sans-serif';
ctx.strokeText('This is a Slide Title.', 450, 420);
```



## 看起來有點像簡報的展示

[example001.html](https://s3-ap-southeast-1.amazonaws.com/fillanoexamples/ironman2016/example001.html)

可以嘗試依序或不依序按下按鈕試試看。依序按下以後，畫面像這樣：

![](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/03-001.png)

另外，這個背景圖片是從Presentation Magazine的免費PowerPoint樣板： [Pencil PowerPoint Template](https://www.presentationmagazine.com/pencil-powerpoint-template-4684.htm) 中解出來的，版權還是屬於Presentation Magazine公司所有喔。