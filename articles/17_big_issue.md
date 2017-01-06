# 17 - 大危機...

在研究Text Box的時候發現，之前使用xml2js從xml轉成Javascript Object的方法有大問題...





## xml2js的問題

### 問題一

這個問題是在看`<a:p>`及它的子元素時發現的...

`<a:p>`可以有三種子元素其中：

1. `<a:pPr>`（段落屬性）只能出現零到一次
2. `<a:r>`（文字展現）可以出現零到多次
3. `<a:br>`（斷行）可以出現零到多次
4. `<a:fld>`（文字欄位）可以出現零到多次
5. `<a:endParaRPr>`（段落結束屬性）只能出現零到一次



這裡面`<a:r>`、`<a:br>`、`<a:fld>`都可以同時出現多次，且順序是有意義的。一個段落裡面可以夾雜文字、斷航跟文字欄位，這些會依照順序呈現。

不過用預設的選項時，xml2js會把每種tag元素集中在一個以tag名稱命名的陣列中...，這樣他們的順序就被破壞了，例如有斷行，但是我們不知道它是發生在哪兩段文字間。

透過改變xml2js的剖析器選項，可以克服這個問題：

```javascript
const xml2js = require('xml2js');
...
let parser = new xml2js.Parser({
	async: true,
    explicitRoot: false,
	preserveChildrenOrder: true, 
	explicitArray: false, 
	explicitChildren: true
});
...
parser.parseString(str, (err, data) => {
	console.log(JSON.stringify(data, null, 2));
});
```

這樣它會產生幾個**額外**結果：

1. 把所有子元素放在$$屬性中，且依照xml中的順序排列
2. 把tag name放在#name屬性中
3. 原本就會把元素屬性放在$屬性中

基本上這就符合資料的需求，雖然因為格式改動，需要重新寫parser來取出pptx的資料。

這時，另外一個問題發生了...

### 問題二

上面有提到，我需要的格式是它轉換後的額外資料，原本的資料還是在...但是，因為資料重複而且各自有不同結構，所以執行JSON.stringify()把資料輸出時，node.js會跑到當掉。32位元的node.js以前有2GB的記憶體限制，處理大檔不時會發生這樣的狀況（例如用伺服器log統計壓測結果，這個時候不是分段解，就是用cluster配合map reduce pattern來解），開始用64位元就沒碰到了，這是我用64位元的node.js第一次碰到這個狀況XD...更麻煩的是，一個xml檔就可能發生，所以也無法每個xml各自處理然後透過stream輸出。





## 目前的解決方式

其實只要解決問題二，問題一是已經可以解的，所以...土炮一個把物件中不要的東西殺光光的函數：

```javascript
let depth = 0;
function strip(obj) {
	depth++;
	console.log('strip entered.', depth);
	for(let i in obj) {
		if(obj.hasOwnProperty(i) && i !== '$' && i !== '$$' && i !== '#name') {
			delete obj[i];
		}
	}
	if(!!obj.$$) {
		let tmp = obj.$$.reduce((pre, cur) => {
			pre.push(strip(cur));
			return pre;
		}, []);
		obj.$$ = tmp;
	}
	depth--;
	return obj;
}
```

我只需要$、$$、跟#name，其他就殺光，然後處理$$中的子元素，一路遞迴下去。depth變數是為了觀察遞迴深度。

先跑一下`node-pptx`：

![16-001.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/16-001.png)

看起來是可以執行...接下來看看罪魁禍首：

![16-003.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/16-003.png)

嗯，遞迴深度到24...應該就是這樣使得轉json出問題。再看一下轉出的檔案：

![16-006.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/16-006.png)

之前的轉出的json檔，大小約642KB，改過格式後，大小稍大，變成682KB，還算好。



## 後續的處理

嗯，基本上就是要重新寫，因為格式已經完全不一樣...先把新的main.json檔複製到node-pptx-parser/test目錄，然後執行`npm test`看看：

![16-007.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/16-007.png)

現在不只測試不過，parser從一開始就不能正常運作XD，接下來就是要慢慢改了...