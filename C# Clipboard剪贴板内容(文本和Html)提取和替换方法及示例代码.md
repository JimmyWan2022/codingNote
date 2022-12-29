# .NET(C#) Clipboard剪贴板内容(文本和Html)提取和替换方法及示例代码

本文主要介绍.NET(C#) 中，通过Clipboard读取剪贴板内容，以及为剪贴板赋值来替换剪贴板中内容，包括文本和Html的复制和粘贴的方法及示例代码。

## 1、C#通过Clipboard读取剪贴板文本和HTML内容

读取剪贴板中数据的操作相当于就是Ctrl+V

```
//获取文件内容
 Clipboard.GetText();
//获取Html内容
Clipboard.GetText(TextDataFormat.Html);
```

## 2、C#通过Clipboard替换写入剪贴板文本和HTML内容

替换剪贴板中数据的操作相当于就是Ctrl+C

1）文本内容

```c#
 Clipboard.SetText("cjavapy.com");
```

2）HTML内容

```c#
public void AddHyperlinkToClipboard(string sHtmlFragment)
{
    const string sContextStart = "<HTML><BODY><!--StartFragment -->";
    const string sContextEnd = "<!--EndFragment --></BODY></HTML>";
    string m_sDescription = "Version:1.0" + Environment.NewLine + "StartHTML:aaaaaaaaaa" + Environment.NewLine + "EndHTML:bbbbbbbbbb" + Environment.NewLine + "StartFragment:cccccccccc" + Environment.NewLine + "EndFragment:dddddddddd" + Environment.NewLine;
    //= "<A HREF=" + "'" + link +"'" + ">" + description + "</A>";
    string sData = m_sDescription + sContextStart + sHtmlFragment + sContextEnd;
    sData = sData.Replace("aaaaaaaaaa", m_sDescription.Length.ToString().PadLeft(10, '0'));
    sData = sData.Replace("bbbbbbbbbb", sData.Length.ToString().PadLeft(10, '0'));
    sData = sData.Replace("cccccccccc", (m_sDescription + sContextStart).Length.ToString().PadLeft(10, '0'));
    sData = sData.Replace("dddddddddd", (m_sDescription + sContextStart + sHtmlFragment).Length.ToString().PadLeft(10, '0'));
    //sData.Dump();
    Clipboard.SetDataObject(new DataObject(DataFormats.Html, sData), true);
}
```

**使用示例：**

```c#
AddHyperlinkToClipboard("<a class=\"w-100 text-nowrap cjavapy_item\" title=\"Python中利用all()来优化减少判断的代码\" href=\"/article/58/\"><span class=\"glyphicon glyphicon-star-empty pr-1\"></span>Python中利用all()来优化减少判断的代码\n                </a>");
```

**注意：**HTML内容剪贴板替换写入之后，需要在富文件编辑器中才能粘贴出来。