# 08 - 使用node.js取得pptx資訊(1) - 建立檔案關連性列表

之前用node.js試寫的程式，感覺還蠻順的，在Javascript直接處理物件結構感覺也比透過OpenXML SDK來的方便，所以先繼續發展下去吧。





## 調整之前寫的程式

之前寫的node-pptx程式，只是單純地把xml檔轉成json檔輸出。但是多個json檔案處理起來其實還是很囉嗦，所以就以檔案名稱當做key，將這些資料合併到一個json檔。

另外，存放在ppt/media中的檔案，都是投影片中使用到的影像檔，而內嵌到投影片中的影音檔等外部媒體，也會放在這個目錄，所以順便把它輸出到media目錄中。

程式變成這樣（index.js）：

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
let media = {};

zip.on('ready', () => {
	let entries = zip.entries();
	let objs = [];
	let meds = [];
	for(let i in entries) {
		if(entries.hasOwnProperty(i)) {
				objs.push(i);
		}
	}
	Promise.all(objs.reduce((pre, cur) => {
		if(cur.indexOf('.rels') > -1 || cur.indexOf('.xml') > -1) {
			let p = new Promise((resolve, reject) => {
				let parser = new xml2js.Parser();
				parser.parseString(zip.entryDataSync(cur).toString(), (err, data) => {
					if(!!err) return reject(err);
					resolve({file: cur, data: data});
				});
			});
			pre.push(p);
		}
		if(cur.indexOf('ppt/media') === 0) {
			let p = new Promise((resolve, reject) => {
				resolve({file: cur, data: zip.entryDataSync(cur)})
			});
			pre.push(p)
		}
		return pre;
	}, []))
	.then(objs => {
		objs.forEach(obj => {
			if(obj.file.indexOf('.rels') > -1 || obj.file.indexOf('.xml') > -1)
				result[obj.file] = obj.data;
		});
		return new Promise((resolve, reject) => {
			fs.writeFile(argv._[1] + path.sep + 'main.json', JSON.stringify(result, null, 2), {encoding: 'utf-8'}, err => {
				if(!!err) return reject(err);
				resolve(objs);
			});
		});
	}, (reason) => {
		console.error(reason);
	})
	.then(objs => {
		return new Promise((resolve, reject) => {
			fs.mkdir(argv._[1] + path.sep + 'media', err => {
				if(!!err) return reject(err);
				resolve(objs);
			})
		});
	}, reason => {
		console.error(reason);
	})
	.then(objs => {
		return Promise.all(objs.filter(entry => {
			if(entry.file.indexOf('ppt/media/') === 0)
				return true;
			return false;
		}).reduce((pre, cur) => {
			let p = new Promise((resolve, reject) => {
				let filename = argv._[1] + path.sep + 'media' + path.sep + cur.file.split(path.sep)[cur.file.split(path.sep).length-1];
				fs.writeFile(filename, cur.data, err => {
					if(!!err) return reject(err);
					resolve();
				});			
			});
			pre.push(p);
			return pre;
		}, []));
	})
	.then(() => {
		console.log('done.');
	});

});

function help() {
	console.log('[Usage] node node-pptx [path to pptx file] [export directory]');
}
```

package.json:

```javascript
{
  "name": "node-pptx",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "minimist": "^1.2.0",
    "node-stream-zip": "^1.3.5",
    "xml2js": "^0.4.17"
  }
}
```

我把檔案放在tools/node-pptx目錄中，所以這樣執行：

```
Feng-Hsu-Pingteki-MacBook-Pro:ironman2016 fillano$ node tools/node-pptx slides/slide1.pptx slide1
done.
Feng-Hsu-Pingteki-MacBook-Pro:ironman2016 fillano$ ls -R slide1
main.json	media

slide1/media:
image1.jpeg	image2.png
```

可以看到，程式讀取slides/slide1.pptx後，在slide1目錄中產生了main.json、media/image1.jpeg、media/image2.png三個檔案。

稍微瞄一下main.json長怎樣：

```json
{
  "[Content_Types].xml": {
    "Types": {
      "$": {
        "xmlns": "http://schemas.openxmlformats.org/package/2006/content-types"
      },
      "Default": [
        {
          "$": {
            "Extension": "xml",
            "ContentType": "application/xml"
          }
        },
        {
          "$": {
            "Extension": "jpeg",
            "ContentType": "image/jpeg"
          }
        },
        {
          "$": {
            "Extension": "bin",
            "ContentType": "application/vnd.openxmlformats-officedocument.presentationml.printerSettings"
          }
        },
        {
          "$": {
            "Extension": "png",
            "ContentType": "image/png"
          }
        },
        {
          "$": {
            "Extension": "rels",
            "ContentType": "application/vnd.openxmlformats-package.relationships+xml"
          }
        }
      ],
      "Override": [
        {
          "$": {
            "PartName": "/ppt/presentation.xml",
            "ContentType": "application/vnd.openxmlformats-officedocument.presentationml.presentation.main+xml"
          }
        },
....後面還很長
```

總之就是以檔名做key，xml轉成json的資料當做內容的格式。





## 開始剖析，先建立檔案關連性列表

為了避免程式太看起來眼花撩亂，剖析資料的部份就另外寫，需要的話未來還是可以做成模組來整合。

pptx中使用副檔名為.rels的xml檔來描述各個部件之間的關聯性。除了根目錄中的_rels/.rels之外，每個.rels檔會跟一個.xml檔匹配。結構很簡單，就是在Relationships之下有多個Relationship tag，Id屬性是會在相對的.xml檔中用來參考的id，Target屬性則是「相對」於.xml檔的關連檔案路徑，所以是一個相對路徑。另外，在使用上需要注意的是，這個Id只對對應的xml檔有效，這跟Target所指定的路徑也是基於.xml檔的相對路徑一樣。

以ppt/presentation.xml.rels轉出來的JSON為例，大致上會像：

```json
{
  "Relationships": {
    "$": {
      "xmlns": "http://schemas.openxmlformats.org/package/2006/relationships"
    },
    "Relationship": [
      {
        "$": {
          "Id": "rId3",
          "Type": "http://schemas.openxmlformats.org/officeDocument/2006/relationships/slide",
          "Target": "slides/slide2.xml"
        }
      },
      {
        "$": {
          "Id": "rId4",
          "Type": "http://schemas.openxmlformats.org/officeDocument/2006/relationships/slide",
          "Target": "slides/slide3.xml"
        }
      },
      {
        "$": {
          "Id": "rId5",
          "Type": "http://schemas.openxmlformats.org/officeDocument/2006/relationships/slide",
          "Target": "slides/slide4.xml"
        }
      },
      {
        "$": {
          "Id": "rId6",
          "Type": "http://schemas.openxmlformats.org/officeDocument/2006/relationships/printerSettings",
          "Target": "printerSettings/printerSettings1.bin"
        }
      },
      {
        "$": {
          "Id": "rId7",
          "Type": "http://schemas.openxmlformats.org/officeDocument/2006/relationships/presProps",
          "Target": "presProps.xml"
        }
      },
      {
        "$": {
          "Id": "rId8",
          "Type": "http://schemas.openxmlformats.org/officeDocument/2006/relationships/viewProps",
          "Target": "viewProps.xml"
        }
      },
      {
        "$": {
          "Id": "rId9",
          "Type": "http://schemas.openxmlformats.org/officeDocument/2006/relationships/theme",
          "Target": "theme/theme1.xml"
        }
      },
      {
        "$": {
          "Id": "rId10",
          "Type": "http://schemas.openxmlformats.org/officeDocument/2006/relationships/tableStyles",
          "Target": "tableStyles.xml"
        }
      },
      {
        "$": {
          "Id": "rId1",
          "Type": "http://schemas.openxmlformats.org/officeDocument/2006/relationships/slideMaster",
          "Target": "slideMasters/slideMaster1.xml"
        }
      },
      {
        "$": {
          "Id": "rId2",
          "Type": "http://schemas.openxmlformats.org/officeDocument/2006/relationships/slide",
          "Target": "slides/slide1.xml"
        }
      }
    ]
  }
}
```

（用來把xml轉成Javascript的xml2js模組，底層是基於SAX，它會固定把Tag的屬性放到名為$的屬性中）

想要做的檔案關連性列表，大概有幾個東西要調整：

1. 同時有Id對Target及Target對Id的列表，這樣知道Target就可以取得Id，知道Id也可以取得Target
2. 把所有的Target調整為一致的絕對路徑，這樣Target跟Key才會一致，會更方便查詢

就把程式取名為node-pptx-parser，放在tools目錄下：

主程式還是index.js：

```javascript
const args = require('minimist')(process.argv.slice(2));
const path = require('path');
const fs = require('fs');

if(args._.length < 1) {
	console.log('Usage: node node-pptx-parser [path to main.json]');
}

let target = args._[0];
if(target.indexOf('main.json') < 0)
	target += path.sep + 'main.json';

fs.readFile(target, {encoding: 'utf-8'}, (err, data) => {
	if(!!err) return console.error(err);
	let pptx = JSON.parse(data);
	let entries = [];
	for(let i in pptx) {
		if(pptx.hasOwnProperty(i)) {
			entries.push(i);
		}
	}
	let relations = relationParser(entries, pptx);
	console.log(JSON.stringify(relations, null, 2));
});

function relationParser(entries, pptx) {
	let relations = {};
	entries.sort().filter(x => {
		if(x.indexOf('ppt/') > -1 && x.indexOf('.rels') > -1)
			return true;
		return false;
	}).forEach(y => {
		pptx[y].Relationships.Relationship.forEach(z => {
			let key = y
			.split(path.sep)
			.filter(u => {
				if(u === '_rels') return false;
				return true;
			})
			.join(path.sep);
			key = key.substr(0, key.length - 5);
			if(!relations[key]) {
				relations[key] = {};
			}
			if(key === 'ppt/presentation.xml') {
				relations[key][z.$.Id] = 'ppt' + path.sep + z.$.Target
				relations[key]['ppt' + path.sep + z.$.Target] = z.$.Id;
			} else {
				relations[key][z.$.Id] = z.$.Target.replace('..', 'ppt');
				relations[key][z.$.Target.replace('..', 'ppt')] = z.$.Id;
			}
		})
	});
	return relations;
}
```

使用的模組只有minimist，所以就不列出package.json了。

來執行看看：

```
Feng-Hsu-Pingteki-MacBook-Pro:ironman2016 fillano$ node tools/node-pptx-parser slide1/main.json
{
  "ppt/presentation.xml": {
    "rId3": "ppt/slides/slide2.xml",
    "ppt/slides/slide2.xml": "rId3",
    "rId4": "ppt/slides/slide3.xml",
    "ppt/slides/slide3.xml": "rId4",
    "rId5": "ppt/slides/slide4.xml",
    "ppt/slides/slide4.xml": "rId5",
    "rId6": "ppt/printerSettings/printerSettings1.bin",
    "ppt/printerSettings/printerSettings1.bin": "rId6",
    "rId7": "ppt/presProps.xml",
    "ppt/presProps.xml": "rId7",
    "rId8": "ppt/viewProps.xml",
    "ppt/viewProps.xml": "rId8",
    "rId9": "ppt/theme/theme1.xml",
    "ppt/theme/theme1.xml": "rId9",
    "rId10": "ppt/tableStyles.xml",
    "ppt/tableStyles.xml": "rId10",
    "rId1": "ppt/slideMasters/slideMaster1.xml",
    "ppt/slideMasters/slideMaster1.xml": "rId1",
    "rId2": "ppt/slides/slide1.xml",
    "ppt/slides/slide1.xml": "rId2"
  },
  "ppt/slideLayouts/slideLayout1.xml": {
    "rId1": "ppt/slideMasters/slideMaster1.xml",
    "ppt/slideMasters/slideMaster1.xml": "rId1"
  },
  "ppt/slideLayouts/slideLayout10.xml": {
    "rId1": "ppt/slideMasters/slideMaster1.xml",
    "ppt/slideMasters/slideMaster1.xml": "rId1"
  },
  "ppt/slideLayouts/slideLayout11.xml": {
    "rId1": "ppt/slideMasters/slideMaster1.xml",
    "ppt/slideMasters/slideMaster1.xml": "rId1"
  },
  "ppt/slideLayouts/slideLayout2.xml": {
    "rId1": "ppt/slideMasters/slideMaster1.xml",
    "ppt/slideMasters/slideMaster1.xml": "rId1"
  },
  "ppt/slideLayouts/slideLayout3.xml": {
    "rId1": "ppt/slideMasters/slideMaster1.xml",
    "ppt/slideMasters/slideMaster1.xml": "rId1"
  },
  "ppt/slideLayouts/slideLayout4.xml": {
    "rId1": "ppt/slideMasters/slideMaster1.xml",
    "ppt/slideMasters/slideMaster1.xml": "rId1"
  },
  "ppt/slideLayouts/slideLayout5.xml": {
    "rId1": "ppt/slideMasters/slideMaster1.xml",
    "ppt/slideMasters/slideMaster1.xml": "rId1"
  },
  "ppt/slideLayouts/slideLayout6.xml": {
    "rId1": "ppt/slideMasters/slideMaster1.xml",
    "ppt/slideMasters/slideMaster1.xml": "rId1"
  },
  "ppt/slideLayouts/slideLayout7.xml": {
    "rId1": "ppt/slideMasters/slideMaster1.xml",
    "ppt/slideMasters/slideMaster1.xml": "rId1"
  },
  "ppt/slideLayouts/slideLayout8.xml": {
    "rId1": "ppt/slideMasters/slideMaster1.xml",
    "ppt/slideMasters/slideMaster1.xml": "rId1"
  },
  "ppt/slideLayouts/slideLayout9.xml": {
    "rId1": "ppt/slideMasters/slideMaster1.xml",
    "ppt/slideMasters/slideMaster1.xml": "rId1"
  },
  "ppt/slideMasters/slideMaster1.xml": {
    "rId11": "ppt/slideLayouts/slideLayout11.xml",
    "ppt/slideLayouts/slideLayout11.xml": "rId11",
    "rId12": "ppt/theme/theme1.xml",
    "ppt/theme/theme1.xml": "rId12",
    "rId1": "ppt/slideLayouts/slideLayout1.xml",
    "ppt/slideLayouts/slideLayout1.xml": "rId1",
    "rId2": "ppt/slideLayouts/slideLayout2.xml",
    "ppt/slideLayouts/slideLayout2.xml": "rId2",
    "rId3": "ppt/slideLayouts/slideLayout3.xml",
    "ppt/slideLayouts/slideLayout3.xml": "rId3",
    "rId4": "ppt/slideLayouts/slideLayout4.xml",
    "ppt/slideLayouts/slideLayout4.xml": "rId4",
    "rId5": "ppt/slideLayouts/slideLayout5.xml",
    "ppt/slideLayouts/slideLayout5.xml": "rId5",
    "rId6": "ppt/slideLayouts/slideLayout6.xml",
    "ppt/slideLayouts/slideLayout6.xml": "rId6",
    "rId7": "ppt/slideLayouts/slideLayout7.xml",
    "ppt/slideLayouts/slideLayout7.xml": "rId7",
    "rId8": "ppt/slideLayouts/slideLayout8.xml",
    "ppt/slideLayouts/slideLayout8.xml": "rId8",
    "rId9": "ppt/slideLayouts/slideLayout9.xml",
    "ppt/slideLayouts/slideLayout9.xml": "rId9",
    "rId10": "ppt/slideLayouts/slideLayout10.xml",
    "ppt/slideLayouts/slideLayout10.xml": "rId10"
  },
  "ppt/slides/slide1.xml": {
    "rId1": "ppt/slideLayouts/slideLayout1.xml",
    "ppt/slideLayouts/slideLayout1.xml": "rId1"
  },
  "ppt/slides/slide2.xml": {
    "rId1": "ppt/slideLayouts/slideLayout2.xml",
    "ppt/slideLayouts/slideLayout2.xml": "rId1"
  },
  "ppt/slides/slide3.xml": {
    "rId1": "ppt/slideLayouts/slideLayout6.xml",
    "ppt/slideLayouts/slideLayout6.xml": "rId1"
  },
  "ppt/slides/slide4.xml": {
    "rId1": "ppt/slideLayouts/slideLayout6.xml",
    "ppt/slideLayouts/slideLayout6.xml": "rId1",
    "rId2": "ppt/media/image2.png",
    "ppt/media/image2.png": "rId2"
  },
  "ppt/theme/theme1.xml": {
    "rId1": "ppt/media/image1.jpeg",
    "ppt/media/image1.jpeg": "rId1"
  }
}
```

可以看到用Console.log輸出的關連性列表，以.xml檔作為key，列出它的Id與檔案路徑的交互參考。





## 下一步

有了關連性的列表，就可以用它開始剖析內容。例如第一張投影片是```ppt/slides/slide1.xml```。透過這個列表就可以查出它使用了```ppt/slideLayouts/slideLayout1.xml```，然後slideLayout1.xml又使用到```ppt/slideMasters/slideMaster1.xml```，slideMaster1.xml使用到的佈景主題是```ppt/theme/theme1.xml```等等，依此類推。

接下來，就要一步一步從presentation.xml開始，先找出slide1.xml，透過關連性找到它使用的theme1.xml、slideMater1.xml、slideLayout1.xml，然後把資料彙整起來，最後成為繪製投影片的參考。

不過一直看資料也很累，明天再回來看動畫，主要是怎麼做出弧線的移動路徑以及加減速的效果。