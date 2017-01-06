# 13 - 複雜的timing結構

本來想要先來改進之前的動畫呈現，不過在這之前還是先整理一下描述投影片中timing的結構，因為這個結構有點複雜，需要想好怎麼整理出最終的物件架構。





## timing的用途

Office OpenXML中，把投影片中所有會發生的動作，都匯集在一個在p:timing的tag中。以之前做過的動畫效果呈現的投影片範例，他的XML長這樣：

```xml
<p:timing>
    <p:tnLst>
        <p:par>
            <p:cTn id="1" dur="indefinite" restart="never" nodeType="tmRoot" xmlns:p14="http://schemas.microsoft.com/office/powerpoint/2010/main">
                <p:childTnLst>
                    <p:seq concurrent="1" nextAc="seek">
                        <p:cTn id="2" dur="indefinite" nodeType="mainSeq">
                            <p:childTnLst>
                                <p:par>
                                    <p:cTn id="3" fill="hold">
                                        <p:stCondLst>
                                            <p:cond delay="indefinite" />
                                        </p:stCondLst>
                                        <p:childTnLst>
                                            <p:par>
                                                <p:cTn id="4" fill="hold">
                                                    <p:stCondLst>
                                                        <p:cond delay="0" />
                                                    </p:stCondLst>
                                                    <p:childTnLst>
                                                        <p:par>
                                                            <p:cTn id="5" presetID="63" presetClass="path" presetSubtype="0" accel="50000" decel="50000" fill="hold" nodeType="clickEffect">
                                                                <p:stCondLst>
                                                                    <p:cond delay="0" />
                                                                </p:stCondLst>
                                                                <p:childTnLst>
                                                                    <p:animMotion origin="layout" path="M 0 0  L 0.25 0  E" pathEditMode="relative" ptsTypes="">
                                                                        <p:cBhvr>
                                                                            <p:cTn id="6" dur="2000" fill="hold" />
                                                                            <p:tgtEl>
                                                                                <p:spTgt spid="5">
                                                                                    <p:txEl>
                                                                                        <p:pRg st="0" end="0" />
                                                                                    </p:txEl>
                                                                                </p:spTgt>
                                                                            </p:tgtEl>
                                                                            <p:attrNameLst>
                                                                                <p:attrName>ppt_x</p:attrName>
                                                                                <p:attrName>ppt_y</p:attrName>
                                                                            </p:attrNameLst>
                                                                        </p:cBhvr>
                                                                    </p:animMotion>
                                                                </p:childTnLst>
                                                            </p:cTn>
                                                        </p:par>
                                                    </p:childTnLst>
                                                </p:cTn>
                                            </p:par>
                                        </p:childTnLst>
                                    </p:cTn>
                                </p:par>
                                <p:par>
                                    <p:cTn id="7" fill="hold">
                                        <p:stCondLst>
                                            <p:cond delay="indefinite" />
                                        </p:stCondLst>
                                        <p:childTnLst>
                                            <p:par>
                                                <p:cTn id="8" fill="hold">
                                                    <p:stCondLst>
                                                        <p:cond delay="0" />
                                                    </p:stCondLst>
                                                    <p:childTnLst>
                                                        <p:par>
                                                            <p:cTn id="9" presetID="42" presetClass="path" presetSubtype="0" accel="50000" decel="50000" fill="hold" nodeType="clickEffect">
                                                                <p:stCondLst>
                                                                    <p:cond delay="0" />
                                                                </p:stCondLst>
                                                                <p:childTnLst>
                                                                    <p:animMotion origin="layout" path="M 0 0  L 0 0.33322  E" pathEditMode="relative" ptsTypes="">
                                                                        <p:cBhvr>
                                                                            <p:cTn id="10" dur="2000" fill="hold" />
                                                                            <p:tgtEl>
                                                                                <p:spTgt spid="6">
                                                                                    <p:txEl>
                                                                                        <p:pRg st="0" end="0" />
                                                                                    </p:txEl>
                                                                                </p:spTgt>
                                                                            </p:tgtEl>
                                                                            <p:attrNameLst>
                                                                                <p:attrName>ppt_x</p:attrName>
                                                                                <p:attrName>ppt_y</p:attrName>
                                                                            </p:attrNameLst>
                                                                        </p:cBhvr>
                                                                    </p:animMotion>
                                                                </p:childTnLst>
                                                            </p:cTn>
                                                        </p:par>
                                                    </p:childTnLst>
                                                </p:cTn>
                                            </p:par>
                                        </p:childTnLst>
                                    </p:cTn>
                                </p:par>
                            </p:childTnLst>
                        </p:cTn>
                        <p:prevCondLst>
                            <p:cond evt="onPrev" delay="0">
                                <p:tgtEl>
                                    <p:sldTgt />
                                </p:tgtEl>
                            </p:cond>
                        </p:prevCondLst>
                        <p:nextCondLst>
                            <p:cond evt="onNext" delay="0">
                                <p:tgtEl>
                                    <p:sldTgt />
                                </p:tgtEl>
                            </p:cond>
                        </p:nextCondLst>
                    </p:seq>
                </p:childTnLst>
            </p:cTn>
        </p:par>
    </p:tnLst>
</p:timing>
```

可以看出，實際上定義動作的p:animMotion這個tag，藏在最深處...當然用人眼來剖析很容易，但是要用程式來剖析，並且最後可以實作出該有的動作及觸發的方式，會需要知道這個結構的各個部件的意義。





## timing資料的組織結構

不過回頭對照一下ECMA-376底下相關的定義，可以發現雖然在看這些tag的時候不太明顯，但是他們還是一個node tree的結構。從p:tnLst開始看一下：

1. p:timing底下只允許有一個子元素，就是p:tnLst
2. p:tnLst的型別是CT_TimeNodeList
3. 他允許的子元素只能從下面數種擇一，最小可以出現一個，最多不限。這些可以分成三類：
   1. 用來組織其他的node，這幾種元素一定會有p:cTn子元素。p:cTn有一個子元素p:childTnLst，型別也是CT_TimeNodeList，整個組織結構就可以這樣用樹狀的方式組織起來。這些元素包含：
      1. p:par（CT_TLTimeNodeParallel）：可以同時啟動的元素。
      2. p:seq（CT_TLTimeNodeSequence）：必須依照順序啟動的元素，上一個元素的動作結束後，下一個才能啟動
      3. p:excl（CT_TLTimeNodeExclusive）：當他啟動時，其他TimeLine的動作都要暫停
   2. 用來定義動作行為的元素，他們的型別名稱都以Behavior結束。包含：
      1. p:anim（CT_TLAnimateBehavior）
      2. p:animClr（CT_TLAnimateColorBehavior）
      3. p:animEffect（CT_TLAnimateEffectBehavior）
      4. p:animMotion（CT_TLAnimateMotionBehavior）
      5. p:animRot（CT_TLAnimateRotationBehavior）
      6. p:animScale（CT_TLAnimateScaleBehavior）
      7. p:cmd（CT_TLCommandBehavior）
      8. p:set（CT_TLSetBehavior）
   3. 跟影音媒體相關的元素，他們底下會有一個固定的子元素p:cMediaNode，而p:cMediaNode底下固定會有兩個子元素，p:cTn跟p:tgtEl。p:tgtEl是這個影音媒體相關的元素，例如Shape。而p:cTn底下又會有p:childTnList
      1. p:audio（CT_TLMediaNodeAudio）
      2. p:video（CT_TLMediaNodeVideo）

分析過後，再回頭去看p:timing底下的xml，就大致上知道他們在描述什麼。以上述的例子來看：

1. 第一層的p:par是根節點
2. 底下的p:seq，應該是說之下的三個p:par是循序的動作。nodeType="mainSeq"，表示當nextCondList中的狀況發生，就會依照順序進行下一個。
3. 第一個p:par（p:cTn的id是3），底下有一個p:stCondList（Start Condition List），底下的p:cond設定是delay為不確定，表示這個p:par不知道什麼時候會執行。因為這個設定，動作暫時不會執行。
4. 再下面的p:par（p:cTn的id是4），底下的p:stCondList，底下的p:cond設定是delay為0，代表後面的動作是立刻執行的
5. 再下面的p:par（p:cTn的id是5），就有設定具體的nodeType是clickEffect，預設分類是路徑，加減速各佔50%時間等等
6. 再底下的節點就是p:animMotion，定義要進行的移動路徑等
7. p:prevCondList以及p:nextCondList定義的條件都是預設的，例如在播放PowerPoint時，左鍵/右鍵就會觸發onPrev以及onNext，然後就會進行mainSeq中的動作

（不過有些地方xsd的定義跟規格中的說明好像不是很一致，也許我的理解有問題，反正還是先用實際的PowerPoint檔及在PowerPoint中展現的效果來做XD）



## 後續處理動畫效果的方式

之前是使用一個按鈕對應一個動畫效果的動作，這只是為了確認動畫效果怎麼實作。要符合p:timing的話，改成「上一步」/「下一步」兩個按鈕會是更好的做法。

另外在資料處理上，cTn是p:par/p:seq/p:excl一定會有的子元素，考慮把結構在壓扁一點的話，其實可以移作p:par/p:seq/p:excl的屬性。

明天先處理一下資料的調整，先手動建立timing的資料，然後讓動畫效果還是可以依照「上一步」/「下一步」的操作執行。至於資料的剖析，還是等到更後面再進行。