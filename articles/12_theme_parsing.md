# 12 - 剖析佈景主題

終於要開始剖析投影片中的資訊。先看看要做什麼...





## 目標

其實目標很簡單，就是要要取出昨天做轉場的案例中，投影片使用的colorScheme以及colorMap這兩樣資料。colorScheme定義在佈景主題(theme)中，colorMap定義在母片(slideMaster)中。

不過要取得這些資訊，基本上就必須能訪問到這些關連的資料：

1. 從投影片的外部關連，可以取得他使用的版面配置(slideLayout)
2. 從版面配置的外部關連，則可取得他使用的母片(slideMaster)
3. 最後，從母片的外部關連，可以取得他使用的佈景主題(theme)

做好的parser，大致上像這樣，首先從之前剖析好的presentation資料中，依據投影片的rid列表，取出各個投影片資料，然後交給實際負責投影片資料剖析的模組處理：

```javascript
module.exports = slidesParser;

function slidesParser(entries, pptx, relations, presentation) {
	return presentation.slideIdList.reduce((pre, cur) => {
		pre[cur.rid] = require('./slideParser')([cur.target], pptx, relations);
		return pre;
	}, {});
}
```

處理時，會根據上述過程，取出關連的資料，從佈景主題找到colorScheme，從母片找到colorMap：

```javascript
module.exports = slideParser;

function slideParser(entries, pptx, relations) {
	let target = entries[0], slide = pptx[target], ext = relations[target];
	let layoutTarget = '';
	for(let i in ext) {
		if(ext.hasOwnProperty(i)) {
			if(i.indexOf('ppt/slideLayouts') === 0) {
				layoutTarget = i;
			}
		}
	}
	let slideLayout = pptx[layoutTarget];
	let extLayout = relations[layoutTarget];
	let masterTarget = ''
	for(let i in extLayout) {
		for(let i in extLayout) {
			if(extLayout.hasOwnProperty(i)) {
				if(i.indexOf('ppt/slideMasters') === 0) {
					masterTarget = i;
				}
			}
		}
	}
	let slideMaster = pptx[masterTarget];
	let extTheme = relations[masterTarget];
	let themeTarget = '';
	for(let i in extTheme) {
		if(extTheme.hasOwnProperty(i)) {
			if(i.indexOf('ppt/theme') === 0) {
				themeTarget = i;
			}
		}
	}
	let theme = pptx[themeTarget];
	let ret = {};
	ret['colorScheme'] = {
		name: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0].$.name,
		dk1: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0]['a:dk1'][0]['a:srgbClr'][0].$.val,
		lt1: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0]['a:lt1'][0]['a:srgbClr'][0].$.val,
		dk2: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0]['a:dk2'][0]['a:srgbClr'][0].$.val,
		lt2: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0]['a:lt2'][0]['a:srgbClr'][0].$.val,
		accent1: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0]['a:accent1'][0]['a:srgbClr'][0].$.val,
		accent2: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0]['a:accent2'][0]['a:srgbClr'][0].$.val,
		accent3: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0]['a:accent3'][0]['a:srgbClr'][0].$.val,
		accent4: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0]['a:accent4'][0]['a:srgbClr'][0].$.val,
		accent5: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0]['a:accent5'][0]['a:srgbClr'][0].$.val,
		accent6: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0]['a:accent6'][0]['a:srgbClr'][0].$.val,
		hlink: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0]['a:hlink'][0]['a:srgbClr'][0].$.val,
		folHlink: theme['a:theme']['a:themeElements'][0]['a:clrScheme'][0]['a:folHlink'][0]['a:srgbClr'][0].$.val
	};
	ret['colorMap'] = slideMaster['p:sldMaster']['p:clrMap'][0].$;
	return ret;
}
```

大致上就這樣，過程很直覺，只是有點囉嗦，因為xml2js用SAX剖析後，就固定產出這樣格式的東西XD





## 測試

其實在這之前，需要先寫測試，不然連開發都有困難...

```javascript
describe('ppt/slides/*.xml parsing test', () => {
	let slides = {};
	before(done => {
		fs.readFile('./test/main.json', {encoding: 'utf-8'}, (err, d) => {
			if(!!err) throw err;
			slides = parser(d).slides;
			done();
		});
	});
	describe('check slides', () => {
		it('check rids', done => {
		let rids = ['rId2', 'rId3', 'rId4', 'rId5'];
		for(let i in slides) {
			if(slides.hasOwnProperty(i)) {
				assert.include(rids, i);
			}
		}
		done();
		});
	});
	describe('check ppt/slides/slide1.xml (rId2)', () => {
		it('check color settings', done => {
			let slide = slides['rId2'];
			let colorScheme = slide.colorScheme;
			let colorMap = slide.colorMap;
			assert.equal(colorScheme[colorMap['tx1']], '2F2B20');
			assert.equal(colorScheme[colorMap['tx2']], '675E47');
			assert.equal(colorScheme[colorMap['bg1']], 'FFFFFF');
			assert.equal(colorScheme[colorMap['bg2']], 'DFDCB7');
			assert.equal(colorScheme[colorMap['accent1']], 'A9A57C');
			assert.equal(colorScheme[colorMap['accent2']], '9CBEBD');
			assert.equal(colorScheme[colorMap['accent3']], 'D2CB6C');
			assert.equal(colorScheme[colorMap['accent4']], '95A39D');
			assert.equal(colorScheme[colorMap['accent5']], 'C89F5D');
			assert.equal(colorScheme[colorMap['accent6']], 'B1A089');
			assert.equal(colorScheme[colorMap['hlink']], 'D25814');
			assert.equal(colorScheme[colorMap['folHlink']], '849A0A');
			done();
		});
	});
});
```

跑出來的結果：

![12-001.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/12-001.png)

看起來沒問題（當然開發過程很多問題）。

更新到github上，travis-ci跟coveralls的結果看起來也OK：

![12-002.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/12-002.png)

## 下一步

接下來的重點，是從母片中取出投影片背景等設定。不過明天先來改進之前動畫的一些缺失。







