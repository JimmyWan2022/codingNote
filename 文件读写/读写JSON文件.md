# [【C# 序列化】Json序列化时中文的字符编码 问题](https://www.cnblogs.com/cdaniu/p/16024229.html)

## 读取JSON文件

##### 依赖 System.Text.Json

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
private void data_init()
        {
    		// 获取当前路径下的config文件夹，下面的init.json文件
			configFileDirectory = Path.Combine(Environment.CurrentDirectory, "config");
            configJsonFile = Path.Combine(configFileDirectory, "init.json");
            //Console.WriteLine(configJsonFile);
            //FileInfo fileInfo = new FileInfo(configJsonFile);
            jsonString = File.ReadAllText(configJsonFile, Encoding.UTF8);
            //Console.WriteLine(jsonString);
            //printText(jsonString);
           
            var document = JsonDocument.Parse(jsonString);
            //var alarmTimeString = document.RootElement.GetProperty("alarmTime").GetProperty("alarmTime").GetString();
            //alarmTime = Int32.Parse(alarmTimeString);
            alarmTime = Int32.Parse(document.RootElement.GetProperty("alarmTime").GetProperty("alarmTime").GetString());


            //var aaa = document.RootElement.GetProperty("alarmTime").GetProperty("remark").GetString();
            //MessageBox.Show(aaa);
            textBox1.Text = alarmTime.ToString();   
}

  public void updateAlarmTime() {
            var jsonNode = JsonNode.Parse(jsonString);
            jsonNode["alarmTime"]["alarmTime"] = alarmTime.ToString();
            JsonSerializerOptions jsonSerializerOptions = new JsonSerializerOptions();
            jsonSerializerOptions.WriteIndented = true;
      		//第一种写法
            //jsonSerializerOptions.Encoder = JavaScriptEncoder.Create(UnicodeRanges.BasicLatin, UnicodeRanges.CjkUnifiedIdeographs );
      		//第二种写法
            jsonSerializerOptions.Encoder = JavaScriptEncoder.Create(UnicodeRanges.All);
            string createText = JsonSerializer.Serialize(jsonNode, jsonSerializerOptions);
            //方法1
            File.WriteAllText(configJsonFile, createText, Encoding.UTF8);
        }
```



## 需要读写的JSON文件

```json
{
  "alarmTime": {
    "alarmTime": "5000",
    "name": "警报持续时间",
    "remark": "单位毫秒，默认推荐5000"
  }}
```

