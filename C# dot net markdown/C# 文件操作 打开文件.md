## 打开excel所在文件夹

```c#
 private void button13_Click_1(object sender, EventArgs e)
        {
            //打开excel所在文件夹
            string filePath = Path.Combine(Environment.CurrentDirectory, "电老练Excel");
            if (!File.Exists(filePath))
            {
                //创建“Excel”文件夹
                DirectoryInfo directoryInfo = new DirectoryInfo(filePath);
                directoryInfo.Create();
            }
            System.Diagnostics.Process.Start(filePath);
        }
```

## 打开日志所在文件夹

```c#
 private void button14_Click_1(object sender, EventArgs e)
        {
            //打开日志所在文件夹
            string filePath = Path.Combine(Environment.CurrentDirectory, "日志");
            if (!File.Exists(filePath))
            {
                //创建“Excel”文件夹
                DirectoryInfo directoryInfo = new DirectoryInfo(filePath);
                directoryInfo.Create();
            }
            System.Diagnostics.Process.Start(filePath);
        }
```
## 删除Excel文件夹里面的指定文件
```c#
  //删除Excel文件夹里面的指定文件
        public static void deleteOneFile(string excelName)
        {
            string filePath = Path.Combine(Environment.CurrentDirectory, "Excel") + "\\" + excelName;
            if (File.Exists(filePath))
            {
                File.Delete(filePath);
            }
        }

```

## 删除文件

```c#
string filePath = Path.Combine(currentDirectory, excelName);
FileInfo fileInfo = new FileInfo(Path.Combine(currentDirectory, excelName));
fileInfo.Delete();
```

