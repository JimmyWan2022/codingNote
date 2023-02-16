[MVVM 工具包简介 - .NET Community Toolkit | Microsoft Learn](https://learn.microsoft.com/zh-cn/dotnet/communitytoolkit/mvvm/)



观测对象：ObservableObject

快捷键：propfull



```
public class User : ObservableObject
{
    private string name;

    public string Name
    {
        get => name;
        set => SetProperty(ref name, value);
    }
}
```

### 自定义propfull

如果需要自定义代码段，

可以进入本地路径C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC#\Snippets\2052\Visual C#，找到propfull.snippet文件，进行修改。

以下是添加属性触发的相关修改：

```
<Code Language="csharp">
    <![CDATA[
    public $type$ $property$
    {
        get => $field$;
        set
        {
            $field$ = value;
            OnPropertyChanged();
        }
    }
    private $type$ $field$;
    $end$]]>
</Code>
```

