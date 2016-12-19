# 04 - 透過OpenXML SDK存取pptx

## 關於資料剖析的初步想法

因為最終要產出的教材，是靜態的html檔案，所以資料最好能轉成成方便Javascript處理的格式，也就是JSON或是.js檔。

既然微軟有OpenXML SDK，透過他來存取pptx檔，取出其中的資訊把它轉成JSON，也許是一個比較快的方法。那麼就先來試試看OpenXML SDK。



## 參考文件及工具

1. 既然是微軟的SDK，MSDN上面一定有工具可以用：[Welcome to the Open XML SDK 2.5 for Office](https://msdn.microsoft.com/zh-tw/library/office/bb448854.aspx)
2. ECMA-376規格書要查閱比較麻煩，幸好有人做了一些整理：[Office Open XML](http://officeopenxml.com/)
3. 還是需要觀察pptx解出來的xml文件，但是這樣很難閱讀，所以需要比較方便的閱讀器，目前先用這個線上的XML Beautifier，除了把XML內容美化，他還有轉成樹狀結構的功能，適當地收放結構，會比較容易見樹又見林：[XML <Beautifier/>](http://xmlbeautifier.com/)



## 我的開發環境

公司電腦是有Visual Studio，但是那是公司電腦...我自己是使用Mac，所以先來試用Mac上的開發工具。微軟的.NET Core可以在OSX上跑，不幸的是...很多套件還不支援，包含OpenXML SDK，所以先來用Mono。另外，Xamarin Studio可以直接開發Mono的專案，而且內建NuGet套件管理工具以及語法完成等方便的功能。就先用它了。

怎麼安裝就不說了，先開個Console專案，用NuGet裝好OpenXML SDK，就可以開始嘗試OpenXML SDK的諸多功能。



## 動手寫個Prober

最初的動機是希望拿到一個pptx檔後，透過這個Prober就可以看到裡面使用的物件結構。其實沒寫完，但是...因為從物件的屬性往下遞迴，所以只要給根物件之後，他會把過Reflection所有屬性以及下一層的物件都印出來，這樣只要給他`PresentationPart`物件就會一直往下跑。

先來看程式。首先是Console的主程式Program.cs：

```c#
using System;
using SlideProbe.Utils;

namespace SlideProbe
{
    class MainClass
    {
        public static void Main(string[] args)
        {
            if (args.Length < 2)
            {
                Console.WriteLine("Usage: mono SlideProbe.exe [mode] [pptx file path]");
                Help();
                return;
            }
            string mode = args[0];
            string file = args[1];
            try
            {
                switch (mode)
                {
                    case "presentation":
                        PresentationInfo pi = new PresentationInfo(file);
                        string[] keys = pi.AllKeys();
                        foreach (string key in keys)
                        {
                            pi.GetInfo(key);
                        }
                        break;
                    case "slide":
                        Console.WriteLine("[slide] mode not done yet.");
                        break;
                    case "layout":
                        Console.WriteLine("[layout] mode not done yet.");
                        break;
                    case "master":
                        Console.WriteLine("[master] mode not done yet.");
                        break;
                    case "theme":
                        Console.WriteLine("[theme] mode not done yet.");
                        break;
                    default:
                        Console.WriteLine("Error: Provided mode not supported.");
                        Help();
                        break;
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
                Console.WriteLine("-----------");
                Console.WriteLine("Usage: mono SlideProbe.exe [mode] [pptx file path]");
                Help();
            }
        }
        private static void Help()
        {
            Console.WriteLine("");
            Console.WriteLine("Supported mode:");
            Console.WriteLine("[presentation] provide info about the presentation file.");
            Console.WriteLine("[slide] provide info about every slides.");
            Console.WriteLine("[layout] provide info about slide layout used by each slide.");
            Console.WriteLine("[master] provide info about slide master used by each slide layout.");
            Console.WriteLine("[theme] provide info about the theme used by each slide layout.");
        }
    }
}

```



負責做探測的程式放在Utils這個命名空間，首先是一個簡單的基底類別InfoBase.cs：

```c#
using System;
using System.Reflection;
using System.Collections.Generic;
using DocumentFormat.OpenXml.Packaging;

namespace SlideProbe.Utils
{
    public class InfoBase
    {
        public HashSet<string> cache;
        public string indent;

        public InfoBase()
        {
            cache = new HashSet<string>();
            indent = "  ";
        }

        public void GetInfo(string key)
        {
            Type type = GetType();
            string member = "Get" + key + "Info";
            try
            {
                MethodInfo mi = type.GetMethod(member);
                if (mi == null)
                {
                    Console.WriteLine("Error(1): Method \"" + member + "\" of class " + type.FullName + " not found.");
                    return;
                }
                mi.Invoke(this, BindingFlags.Default, null, null, null);
            }
            catch (Exception ex)
            {
                if (ex.InnerException != null)
                {
                    Console.WriteLine("Error: " + ex.InnerException.Message);
                }
                else
                {
                    Console.WriteLine("Error: " + ex.Message);
                }
            }
        }

        public void ShowProperties(object o)
        {
            ShowProperties(o, "");
        }

        protected void ShowProperties(object o, string prefix)
        {
            if (null == o)
            {
                return;
            }
            Type type = o.GetType();
            //Console.WriteLine("");
            Console.BackgroundColor = ConsoleColor.Blue;
            Console.ForegroundColor = ConsoleColor.White;
            Console.Write(prefix + type.FullName + " : " + type.BaseType.FullName);
            Console.BackgroundColor = ConsoleColor.Black;
            Console.ResetColor();
            if (!cache.Contains(o.GetType().FullName))
            {
                cache.Add(o.GetType().FullName);
                Console.WriteLine("");
            }
            else
            {
                Console.Write(" (type probed.)");
                Console.WriteLine("");
                return;
            }
            foreach (PropertyInfo pi in type.GetProperties())
            {
                if (pi.CanRead)
                {
                    int l = pi.GetIndexParameters().Length;
                    if (l > 0)
                    {
                        //Console.Write(prefix + "1[Indexed]\t" + pi.Name + ": ");
                        Console.Write(prefix + indent + Decorate(pi.Name) + ": ");
                        Console.ForegroundColor = ConsoleColor.Gray;
                        Console.Write("(Indexed Property)");
                    }
                    else
                    {
                        var value = pi.GetValue(o, null);
                        if (null != value)
                        {
                            Type vt = value.GetType();
                            if (vt.FullName.Contains("DocumentFormat.OpenXml"))
                            {
                                if (vt.FullName.Contains("DocumentFormat.OpenXml.Packaging.OpenXmlPartContainer"))
                                {
                                    Console.BackgroundColor = ConsoleColor.Black;
                                    //Console.Write(prefix + "7[Part]" + indent + pi.Name + " (" + vt.FullName + ") : ");
                                    Console.Write(prefix + indent + Decorate(pi.Name) + ": ");

                                    var pairs = value as IEnumerable<IdPartPair>;

                                    if (pairs != null)
                                    {
                                        Console.WriteLine("");
                                        foreach (var pair in (IEnumerable<IdPartPair>)value)
                                        {
                                            if (pair.OpenXmlPart != null)
                                            {
                                                ShowDetail(o, pair.OpenXmlPart, prefix);
                                            }
                                            else
                                            {
                                                //Console.Write(prefix + "6" + indent + pi.Name + ": ");
                                                Console.Write(prefix + indent + Decorate(pi.Name) + ": ");
                                            }
                                        }
                                    }
                                }
                                else
                                {
                                    Console.BackgroundColor = ConsoleColor.Black;
                                    //Console.Write(prefix + "5" + indent + pi.Name + ": ");
                                    Console.Write(prefix + indent + Decorate(pi.Name) + ": ");
                                    if (value != null)
                                    {
                                        ShowDetail(o, value, "");
                                    }
                                    else
                                    {
                                        //Console.Write(prefix + "6" + indent + pi.Name + ": ");
                                        Console.Write(prefix + indent + Decorate(pi.Name) + ": ");
                                    }
                                }
                            }
                            else
                            {
                                //Console.Write(prefix + "2[Others]" + indent + pi.Name + ": ");
                                Console.Write(prefix + indent + Decorate(pi.Name) + ": ");
                                Console.ForegroundColor = ConsoleColor.Gray;
                                Console.Write(value);
                                Console.ResetColor();
                            }
                        }
                        else {
                            //Console.Write(prefix + "3[Null]" + indent + pi.Name + ": ");
                            Console.Write(prefix + indent + Decorate(pi.Name) + ": ");
                        }
                    }
                }
                else 
                {
                    //Console.Write(prefix + "4[WriteOnly]" + indent + pi.Name + ": ");
                    Console.Write(prefix + indent + Decorate(pi.Name) + ": ");
                }
                Console.WriteLine("");
            }
        }

        protected void ShowDetail(object o, object value, string prefix)
        {
            if (value != null)
            {
                Console.ForegroundColor = ConsoleColor.Gray;
                Type sub = o.GetType();
                if (sub.IsPrimitive)
                {
                    Console.Write(value);
                    Console.ResetColor();
                }
                else
                {
                    Console.ResetColor();
                    ShowProperties(value, prefix + indent);
                }
            }
        }

        protected string Decorate(string name)
        {
            return "[" + name + "]";
        }
    }
}

```

為了避免程式跑到堆疊溢位...裡面加上了一些限制：

1. 只有屬於DocumentFormat.OpenXml之下的物件，才會做進一步的探查，否則只會列出值
2. 程式會記錄有探查過的類別，只要有做過探查，就不會做下去...（裡面似乎有類別會有循環參考...）

然後是實際要呼叫的類別，因為要從PresentationPart開始探查，所以叫做PresentationInfo.cs：

```c#
using System;
using System.Reflection;
using DocumentFormat.OpenXml;
using DocumentFormat.OpenXml.Presentation;
using DocumentFormat.OpenXml.Packaging;

namespace SlideProbe.Utils
{
    public class PresentationInfo : InfoBase
    {
        private string file;
        public PresentationDocument ppt;

        public PresentationInfo(string _file)
        {
            file = _file;
            ppt = PresentationDocument.Open(file, false);
        }

        public string[] AllKeys()
        {
            return new string[] {
                "PresentationPart"/*,
                "Presentation"*/
            };
        }

        public void GetPresentationPartInfo()
        {
            ShowProperties(ppt.PresentationPart);
        }

        public void GetPresentationInfo()
        {
            ShowProperties(ppt.PresentationPart.Presentation);
        }
    }
}
```

因為主要的工作其實在InfoBase.cs做完了，所以程式只有一點。另外Presentation其實透過PresentationPart.Presentation就會找到，所以就直接放棄。

程式跑起來像這樣：

![程式開始](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/04-001.png)

最後一行結果：

![最後一行](https://s3-ap-southeast-1.amazonaws.com/fillanoimages/ironman2016/04-002.png)

另外用`wc -l`統計了一下，包含空行總共輸出了5243行...

藍底白字的是類別的資訊，方括號框起來的是Property Name。另外，有一個特別的Property名字通常叫做「Parts」，這裡面會有不同Part文件的關聯性列表。在InfoBase.cs裡面有特別處理。

程式中沒有列出「方法」...因為重複的太多，所以就省略了。不過有一個方法Descends()最好是一開始就知道的，後面會再提到。

通過觀察，可以發現OpenXML SDK的架構大概是像這樣：

1. pptx裡面有許多的xml文件，OpenXML SDK通常會使用兩種類別來處理，以根的PresentationPart為例：
   1. PresentationPart負責文件之間的關係，文件本身的資料，在`PresentationPart.Presentation`中
   2. PresentationPart的完整類別名稱是`DocumentFormat.OpenXml.Packaging.PresentationPart`
   3. Presentation的完整類別名稱是`DocumentFormat.OpenXml.Presentation.Presentation`
2. 其他的類別也幾乎都有這樣的對應關係，例如SlidePart跟Slide、SlideLayoutPart跟SlideLayout、SlideMaster跟SlideMaster等等
3. 其實之前文章中有提到，_rels目錄中會有一個跟上層目錄中的xml相對應的檔案，只是檔名在.xml後面又加了.rels。這個檔案是用來描述與這個xml相關的其他xml檔的關聯。



## 使用OpenXML SDK的優缺點

### 優點

1. 架構還蠻清楚的，而且跟pptx中的檔案可以對應起來
2. 程式不是很難寫，只是有些地方有點囉嗦
3. 如果使用.NET技術來開發網站，會比較容易整合（不過這不是我目前需要的XD）

### 缺點

1. 如果不熟Office OpenXML的話，其實不好上手，而且如果不是要直接處理pptx檔而是有其他目的，用起來有點麻煩
2. 關鍵在於，透過這些可以直接探查到的類別，只能碰到xml檔的一些基本資訊，真正需要的簡報資訊例如使用的圖形、圖片、文字、顏色對應、字型等等，都不會放在這些類別的屬性中，必須透過Descends方法去查詢（有點像DOM的getElementsByTagName()方法），或是透過ChildElements、FirstChild、LastChild等屬性一層一層查訪下去，這樣要做轉檔還有點小麻煩...

之後也用node.js來做做看，不過這樣就要直接碰xml檔XD

另外，程式檔我打包了，可以直接下載來用用看：[SlideProbe.zip](https://s3-ap-southeast-1.amazonaws.com/fillanofiles/ironman2016/SlideProbe.zip)