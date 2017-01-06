# 18 - 重寫pptx parser

昨天發生重大危機，只好開始重寫pptx xml轉成目標json檔的剖析器。





## json檔的格式變化

簡單地說，現在json檔中每個物件只會有三個屬性：

1. \#name：字串，存放物件的tagName
2. $：物件，存放物件的屬性
3. $$：陣列：存放子tag的物件



另外，xml2js調整過設定，子tag的物件陣列，會依照它出現在xml中的順序排列。





## 重寫parser

剖析檔案關係的部份由於xml結構很簡單，所以修改不大，馬上就測試通過。

複雜一點的是ppt/presentation.xml（簡報）轉出的json。這裡面有幾項關於文字樣式的資訊，放在p:defaultTextStyle底下。裡面有：

1. a:defPPr：從字面上來看，是預設的段落文字屬性。
2. 從a:lvl1pPr到a:lvl9pPr：這是為了一到九級的文字設定的段落文字屬性

這兩個的xml資料型別，都是CT_TextParagraphProperties。而CT_TextParagraphProperties底下有一個子元素，a:defRPr，它的xml資料型別是CT_TextCharacterProperties。這兩個資料型別，在後續的資料處理中會持續使用到。他們各自的作用：

1. CT_TextParagraphProperties：定義跟段落相關的樣式屬性，例如左右對齊方式、垂直對齊方式、行高、左右間距、縮排、列表級數、列表符號等
2. CT_TextParagraphProperties：定義段落中個別文字的樣式屬性，例如大小、語系、字型、顏色、粗體/斜體等等

母片、版面配置、投影片中，每個圖形中的文字樣式，都可能會繼承這些預設值或是重新定義，所以只要是使用預設的版面配置圖形（標題、內容等），就必需按照簡報=>母片=>版面配置=>投影片的順序處理一遍資料，才知道某個段落以及其中某幾個文字的呈現方式。

基於後續的需求，乾脆先在做ppt/presentation.xml剖析時先把這部份功能做起來。

基本的parser.js，放在lib目錄中：

```javascript
module.exports = {
	textParagraphPropertiesParser: textParagraphPropertiesParser,
	textCharacterPropertiesParser: textCharacterPropertiesParser
};

function textParagraphPropertiesParser(def, obj) {
	if('undefined' === typeof def || def === null) {
		def = {
			/* attributes */
			algn: null,
			defTabSz: null,
			eaLnBrk: null,
			fontAlgn: null,
			hangingPunct: null,
			indent: null,
			latinLnBrk: null,
			lvl: null,
			marL: null,
			marR: null,
			rtl: null,
			/* child elements */
			lnSpc: null,
			spcBef: null,
			spcAft: null,
			tabLst: null,
			defRPr: null,
			extLst: null,
			buClr: null,
			buSzPts: null,
			buFont: null,
			buChar: null,
			buNone: false
		};
	}
	let attr = ['algn', 'defTabSz', 'eaLnBrk', 'fontAlgn', 'hangingPunct', 'indent', 'latinLnBrk', 'lvl', 'marL', 'marR', 'rtl'];
	let chld = ['a:lnSpc', 'a:spcBef', 'a:spcAft', 'a:tabLst', 'a:defRPr', 'a:extLst', 'a:buClr', 'a:buSzPts', 'a:buFont', 'a:buChar'];
	let chldParser = {
		'a:defRPr': o => textCharacterPropertiesParser(null, o),
		'a:spcBef': o => commonChldParser(o, 'a:spcPct'),
		'a:spcAft': o => commonChldParser(o, 'a:spcPct'),
		'a:buClr': o => commonChldParser(o, 'a:schemeClr'),
		'a:buFont': o => o.$.typeface,
		'a:buChar': o => o.$.char,
		'a:buNone': () => true
	};

	Object.keys(def).forEach(a => {
		if(attr.indexOf(a) > -1) {
			if(!!obj && !!obj.$ && !!obj.$[a]) {
				def[a] = obj.$[a];
			}
		}
		if(chld.indexOf('a:'+a) > -1) {
			if(!!obj && !!obj.$$) {
				let t = obj.$$.filter(b => {return b['#name'] === 'a:'+a});
				if(!!t && t.length === 1) {
					if(!!chldParser[t[0]['#name']]) {
						def[a] = chldParser[t[0]['#name']](t[0]);
					}
				}
			}
		}
	});
	return def;
}
function commonChldParser(o, n) {
	if(!!o && !!o.$$) {
		let t = o.$$.filter(z => z['#name'] === n);
		if(!!t && t.length === 1) {
			return t[0].$.val;
		}
	}
	return null;
}
function textCharacterPropertiesParser(def, obj) {
	if(typeof def === 'undefine' || def === null) {
		def = {
			/* attributes */
			altLang: null,
			b: null,
			baseline: null,
			bmk: null,
			cap: null,
			dirty: null,
			err: null,
			i: null,
			kern: null,
			kumimoji: null,
			lang: null,
			noProof: null,
			normalizeH: null,
			smtClean: null,
			spc: null,
			strike: null,
			sz: null,
			u: null,
			/* child elements */
			ln: null,
			solidFill: null,
			effectLst: null,
			highlight: null,
			uLn: null,
			uFill: null,
			latin: null,
			ea: null,
			cs: null,
			sym: null,
			hlinkClick: null,
			hlinkMouseOver: null,
			rtl: null,
			extLst: null
		};
	}
	let attr = ['altLang', 'b', 'baseline', 'bmk', 'cap', 'dirty', 'err', 'i', 'kern', 'kumimoji', 'lang', 'noProof', 'normalizeH', 'smtClean', 'spc', 'strike', 'sz', 'u'];
	let chld = ['a:ln', 'a:solidFill', 'a:effectLst', 'a:highlight', 'a:uLn', 'a:uFill', 'a:latin', 'a:latin', 'a:ea', 'a:cs', 'a:sym', 'a:hlinkClick', 'a:hlinkMouseOver', 'a:rtl', 'a:extLst'];
	let chldParser = {
		'a:latin': o => o.$.typeface,
		'a:ea': o => o.$.typeface,
		'a:cs': o => o.$.typeface,
		'a:solidFill': o => commonChldParser(o, 'a:schemeClr')
	};
	Object.keys(def).forEach(a => {
		if(attr.indexOf(a) > -1) {
			if(!!obj && !!obj.$ && !!obj.$[a]) {
				def[a] = obj.$[a];
			}
		}
		if(chld.indexOf('a:'+a) > -1) {
			if(!!obj && !!obj.$$) {
				let t = obj.$$.filter(b => {return b['#name'] === 'a:'+a});
				if(!!t && t.length === 1) {
					if(!!chldParser[t[0]['#name']]) {
						def[a] = chldParser[t[0]['#name']](t[0]);
					}
				}
			}
		}
	});
	return def;
}
```

然後在剖析ppt/presentation.xml中的p:defaultTextStyle時呼叫：

```javascript
module.exports = presentationParser;

const parsers = require('./parsers');

function presentationParser(entries, pptx, relations) {
	let presentation = {};
	let root = pptx['ppt/presentation.xml'].$$;
	presentation['slideMasterIdList'] = [];
	presentation['slideIdList'] = [];

	root.forEach(z => {
		switch(z['#name']) {
			case 'p:sldMasterIdLst':
				z.$$.forEach(a => {
					let rid = a.$['r:id'];
					let target = relations['ppt/presentation.xml'][rid];
					presentation['slideMasterIdList'].push({id: a.$.id, rid: rid, target: target});
				});
			break;
			case 'p:sldIdLst':
				z.$$.forEach(b => {
					let rid = b.$['r:id'];
					let target = relations['ppt/presentation.xml'][rid];
					presentation['slideIdList'].push({id: b.$.id, rid: rid, target: target});
				});
			break;
			case 'p:sldSz':
				presentation['slideSize'] = {
					type: z.$.type,
					cx: z.$.cx,
					cy: z.$.cy
				};
			break;
			case 'p:defaultTextStyle':
				presentation.defaultTextStyle = {};
				z.$$.forEach(c => {
					switch(c['#name']) {
						case 'a:defPPr':
							presentation.defaultTextStyle.defaultParagraphStyle = parsers.textParagraphPropertiesParser(null, c);
						break;
						default:
							let tagName = '';
							for(let i=1; i<10; i++) {
								if(c['#name'] === 'a:lvl' + i + 'pPr') {
									presentation.defaultTextStyle['level' + i + 'ParagraphProperty'] = parsers.textParagraphPropertiesParser(presentation.defaultTextStyle.defaultParagraphStyle, c);
								}
							}
						break;
					}
				});
			break;
		}
	});
	return presentation;
}
```

剖析母片及投影片列表，還有投影片大小等資料的部分，只微調一下就能動了。

之前做到投影片的剖析，重新寫了以後只完成到簡報的部份，所以先把投影片部分的測試註解起來，然後跑測試看看：

![18-001.png](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/18-001.png)

除了註解掉，基本上程式沒改。不過也是因為之前沒有寫defaultTextStyle的測試XD

另外，覆蓋率比較差，是因為母片、版面配置、投影片都還沒處理到，所以一些程式不會跑到。





## 後面的想法

需要先趕一下進度。原本是希望這時候已經基本上把投影片中的圖形處理完了，不過看樣子還得花幾天。總之，目標是要能把剖析處理過的資料在html中實地呈現，希望有時間順利完成阿XD

