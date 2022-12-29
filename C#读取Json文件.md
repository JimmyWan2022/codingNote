

## C#读取Json文件

## 依赖 System.Text.Json;

在NuGet包中就能下载

###  获取当前exe所在路径

```c#
string currentDir  = Environment.CurrentDirectory;
```

路径字符串拼接函数

```
string  configJsonFile = Path.Combine(configFileDirectory, "init.json");
```



```c#
-root
--config
---init.json
-main.exe
    
using System;
using System.IO;
using System.Text;
using System.Text.Encodings.Web;
using System.Text.Json;
using System.Text.Json.Nodes;
using System.Text.RegularExpressions;
using System.Text.Unicode;

string configFileDirectory = Path.Combine(Environment.CurrentDirectory, "config");
string configJsonFile = Path.Combine(configFileDirectory, "init.json");
string jsonString = File.ReadAllText(configJsonFile);
var document = JsonDocument.Parse(jsonString);
textBox2.Text = document.RootElement.GetProperty("number1").GetProperty("number2").GetString();
```

init.json

```json
{
  "number1": {
    "number2": "5000",
    "name": "中文1",
    "remark": "中文2"
  },
  "number3": {
    "number4": "5001",
    "name": "中文3",
    "remark": "中文4"
  },
}
```

## JSON输出，解决中文乱码问题



```c#
using System;
using System.IO;
using System.Text;
using System.Text.Encodings.Web;
using System.Text.Json;
using System.Text.Json.Nodes;
using System.Text.RegularExpressions;
using System.Text.Unicode;
using System.Windows.Forms;

//修改number1的值，输出到JSON中
public void updateAlarmTime() {
            var jsonNode = JsonNode.Parse(jsonString);
            jsonNode["number1"]["number1"] = alarmTime.ToString();
            JsonSerializerOptions jsonSerializerOptions = new JsonSerializerOptions();
            jsonSerializerOptions.WriteIndented = true;
            jsonSerializerOptions.Encoder = JavaScriptEncoder.Create(UnicodeRanges.All);
            string createText = JsonSerializer.Serialize(jsonNode, jsonSerializerOptions);
            //方法1
            File.WriteAllText(configJsonFile, createText, Encoding.UTF8);
        }
```

## JSON内容修改，并解决中文乱码问题



configJsonFile 为  E:\smartJG\03开发\02代码\XJGApp1\XJGApp1\bin\Debug\config\init.json

```c#
private void button3_Click(object sender, EventArgs e)
        {
            // 把数据写入json文件
            var jsonNode = JsonNode.Parse(jsonString);
            jsonNode["alarmTime"]["alarmTime"] = alarmTime.ToString() ;
            JsonSerializerOptions jsonSerializerOptions = new JsonSerializerOptions();
            jsonSerializerOptions.WriteIndented = true;
            //jsonSerializerOptions.Encoder = JavaScriptEncoder.Create(UnicodeRanges.BasicLatin, UnicodeRanges.CjkUnifiedIdeographs );
    // 解决中文输出问题，防止输出\u3721\u9862这种类似的UTF8格式，可读性低
            jsonSerializerOptions.Encoder = JavaScriptEncoder.Create(UnicodeRanges.All);
    		// 序列化
            string createText = JsonSerializer.Serialize(jsonNode, jsonSerializerOptions);
            //输出到文件
            File.WriteAllText(configJsonFile, createText, Encoding.UTF8);
        }
```



## 参考

1.中文乱码参考：https://www.cnblogs.com/cdaniu/p/15969988.html

