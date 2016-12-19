# 06 - 使用node.js取得pptx資訊 - 初步剖析

用OpenXML SDK嘗試存取pptx檔取出一些資訊後，想再用node.js嘗試看看可以怎麼做。



## 使用xml2js模組把xml轉成Javascript物件

上網找了一下，發現有個xml2js套件，可以直接把.xml轉成Javascript物件...有這個我還需要透過OpenXML SDK來抓出資料再轉成JSON嗎？

套件網址： [node-xml2js](https://github.com/Leonidas-from-XIV/node-xml2js)

先來嘗試一下，安裝很簡單，只要```npm install xml2js```。大致上步驟：

1. ```mkdir op```
2. ```cd op```
3. ```npm init```，沒有要發行，所以都用預設值
4. ```npm install —save xml2js```，這樣哪天忘記了，執行```npm install```就可以直接把使用的套件裝回來
5. ```npm install —save minimist```，我習慣用這個處理命令列參數

接下來打開index.js，寫一個簡單的程式來看看抓到怎樣的東西：

```javascript
const args = require('minimist')(process.argv.slice(2));
const parser = require('xml2js');
const fs = require('fs');

if(args._.length !== 1) {
	console.error('Please provide a file name. Usage: node op [xml file path].');
	process.exit();
}

let file = args._[0];

fs.readFile(file, {encoding: 'utf8'}, (err, data) => {
    if(!!err) return console.error(err.toString());
    let obj = parser.parseString(data, (err, result) => {
        if(!!err) return console.error(err.toString());
        console.log(JSON.stringify(result, null, 2));
    });
});

```

只是試用，先不處理pptx的解壓縮，所以先用zip把pptx檔解到一個目錄中，例如file目錄。然後執行

```
>node op file/ppt/presentation.xml
```

然後看看執行結果：

![06-001.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/06-001.png)

![06-002.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/06-002.png)

看起來就是直接依照xml的結構把資料轉過來，tag的屬性會放入物件的$屬性中，子元素則放在以子元素標籤為名稱的屬性中，通常是一個陣列。

結果雖然有點囉嗦，但是看起來還蠻簡單的，而且規則很清楚，應該不難進一步處理。



## 使用node-stream-zip模組解壓縮pptx檔

既然pptx檔是一個zip檔，那要處理它首先還是要解壓縮。所以再找找看有什麼可以處理的模組。

印象中以前好像有使用過處理stream的zip模組，但是不知道現在還有沒有，所以上網用node zip stream關鍵字找一下。看起來node-stream-zip這個模組符合我的需求。

套件網址：[node-stream-zip](https://github.com/antelle/node-stream-zip)

安裝也差不多，只要執行```npm install node-stream-zip```就可以。

接下來就試試看寫個程式來解開pptx檔，然後把所有的xml轉成json：

1. ```mkdir node-pptx```
2. ```cd node-pptx```
3. ```npm init```
4. ```npm install —save minimist```
5. ```npm install —save node-stream-zip```
6. ```npm install —save xml2js```

然後開始寫簡單的程式把解壓縮跟xml轉js的過程串起來：

```javascript
const argv = require('minimist')(process.argv.slice(2));

if(argv._.length < 2) {
	help();
	process.exit();
}

const xml2js = require('xml2js')
, StreamZip = require('node-stream-zip')
, zip = new StreamZip({file: argv._[0], storeEntries: true})
, path = require('path')
, fs = require('fs');

let result = {};

zip.on('ready', () => {
	let entries = zip.entries();
	let objs = [];
	for(let i in entries) {
		if(entries.hasOwnProperty(i)) {
			if(i.indexOf('.rels') > -1 || i.indexOf('.xml') > -1)
				objs.push(i);
		}
	}
	Promise.all(objs.reduce((pre, cur) => {
		let p = new Promise((resolve, reject) => {
			let parser = new xml2js.Parser();
			parser.parseString(zip.entryDataSync(cur).toString(), (err, data) => {
				if(!!err) return reject(err);
				resolve({file: cur, data: data});
			});
		});
		pre.push(p);
		return pre;
	}, []))
	.then((objs) => {
		objs.forEach((obj) => {
			let target = obj.file.split(path.sep);
			fs.writeFile(argv._[1] + path.sep + target[target.length-1] + '.json', JSON.stringify(obj.data, null, 2), (err) => {
				if(!!err) return console.error(err);
				console.log(obj.file + ' => ' + argv._[1] + path.sep + target[target.length-1] + '.json');
			});
		});
	}, (reason) => {
		console.error(reason);
	});
});

function help() {
	console.log('[Usage] node node-pptx [path to pptx file] [export directory]');
}
```

然後測試一下：

1. ```mkdir pptxfiles```
2. ```node tools/node-pptx slides/slide1.pptx```
3. ```ls -al pptxfiles```

就可以看哪些xml被轉出到json檔：

![06-003.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/06-003.png)

看一下產生哪些json檔（_rels/.rels在UNIX系統下是隱藏檔，所以...）

![06-004.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/06-004.png)

再確認一下檔案內容：

![06-005.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/06-005.png)

看起來沒問題。

需要說明一下，如果單純轉檔，程式不太需要使用Promise，但是如果是要剖析從xml轉出來的Javascript物件，因為之間互相有關連，所以需要等所有xml轉好才處理，這樣用Promise來處理比較方便。後續的處理，就留待後面幾天吧。明天先來嘗試做動畫。

