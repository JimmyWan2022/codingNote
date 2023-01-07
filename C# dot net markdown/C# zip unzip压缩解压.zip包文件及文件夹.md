# .NET Core(C#) zip unzip压缩解压.zip包文件及文件夹

本文主要介绍.NET Core中，使用ZipArchive压缩解压.zip包文件及文件夹，以使用.NET Core调用linux中zip和unzip命令压缩解压.zip包。

**1､使用ZipArchive类实现解压.zip包中指定文件夹中内容并压缩**

解压source-file.zip中dist文件夹下的所有内容压缩存储在destination-file.zip中。

```c#
 using (MemoryStream srcMemoryStream = new MemoryStream())
        {
            using (MemoryStream targetMemoryStream = new MemoryStream())
            {
                // 为了得到一个字节数组，我只需读取一个文件并将其存储到memory stream
                using (FileStream sourceZipFile = new FileStream(@"f:\source-file.zip", FileMode.Open))
                {
                    sourceZipFile.CopyTo(srcMemoryStream);
                }
                using (ZipArchive srcArchive = new ZipArchive(srcMemoryStream, ZipArchiveMode.Read))
                {
                    using (ZipArchive destArchive = new ZipArchive(targetMemoryStream, ZipArchiveMode.Create, true))
                    {
                        srcArchive.Entries
                            .Where(entry => entry.FullName.Contains("dist/"))
                            .ToList()
                            .ForEach((entry) =>
                            {
                                //我只是在其他归档文件中创建具有相同结构的相同文件夹
                                //如果你想改变结构，你必须重命名或删除部分
                                //路径如下
                                ///  var newEntryName = entry.FullName.Replace("files/dist/", "new-dist/");
                                ///  ZipArchiveEntry newEntry = destArchive.CreateEntry(newEntryName);
                                ZipArchiveEntry newEntry = destArchive.CreateEntry(entry.FullName);
                                using (Stream srcEntry = entry.Open())
                                {
                                    using (Stream destEntry = newEntry.Open())
                                    {
                                        srcEntry.CopyTo(destEntry);
                                    }
                                }
                            });
                    }
                }
                //写了压缩文件在磁盘上，以确保它的工作
                //在这行代码之前，结果字节数组在targetMemoryStream内存流中
                using (FileStream fs = new FileStream(@"f:/destination-file.zip", FileMode.Create))
                {
                    targetMemoryStream.WriteTo(fs);
                    targetMemoryStream.Flush();
                    fs.Flush(true);
                }
            }
        }
```

**2､创建.zip文件**

**创建新条目并使用流对其进行写入。**



```c#
using System;
using System.IO;
using System.IO.Compression;
namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            using (FileStream zipToOpen = new FileStream(@"c:\users\exampleuser\release.zip", FileMode.Open))
            {
                using (ZipArchive archive = new ZipArchive(zipToOpen, ZipArchiveMode.Update))
                {
                    ZipArchiveEntry readmeEntry = archive.CreateEntry("Readme.txt");
                    using (StreamWriter writer = new StreamWriter(readmeEntry.Open()))
                    {
                            writer.WriteLine("Information about this package.");
                            writer.WriteLine("========================");
                    }
                }
            }
        }
    }
}
```

**3､解压.zip文件**

**打开zip归档文件并遍历条目集合。**



```c#
using System;
using System.IO;
using System.IO.Compression;
class Program
{
    static void Main(string[] args)
    {
        string zipPath = @".\result.zip";
        Console.WriteLine("Provide path where to extract the zip file:");
        string extractPath = Console.ReadLine();
        // Normalizes the path.
        extractPath = Path.GetFullPath(extractPath);
        // 确保提取路径上的最后一个字符
        //是目录分隔符char。
        //如果没有这个，恶意的zip文件可能会试图在预期的范围之外遍历
        //提取路径。
        if (!extractPath.EndsWith(Path.DirectorySeparatorChar.ToString(), StringComparison.Ordinal))
            extractPath += Path.DirectorySeparatorChar;
        using (ZipArchive archive = ZipFile.OpenRead(zipPath))
        {
            foreach (ZipArchiveEntry entry in archive.Entries)
            {
                if (entry.FullName.EndsWith(".txt", StringComparison.OrdinalIgnoreCase))
                {
                    // 获取完整路径，以确保删除了相关段。
                    string destinationPath = Path.GetFullPath(Path.Combine(extractPath, entry.FullName));
                    //序号匹配是最安全的，区分大小写的卷可以装入
                    //是不区分大小写的。
                    if (destinationPath.StartsWith(extractPath, StringComparison.Ordinal))
                        entry.ExtractToFile(destinationPath);
                }
            }
        }
    }
}
```

**4､更新.zip包文件中内容**

用扩展方法从现有文件在zip存档中创建新条目并提取存档内容。须引用`System.IO.Compression.FileSystem`程序集才能执行代码。

```c#
using System;
using System.IO;
using System.IO.Compression;
namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            string zipPath = @"c:\users\exampleuser\start.zip";
            string extractPath = @"c:\users\exampleuser\extract";
            string newFile = @"c:\users\exampleuser\NewFile.txt";
            
            using (ZipArchive archive = ZipFile.Open(zipPath, ZipArchiveMode.Update))
            {
                archive.CreateEntryFromFile(newFile, "NewEntry.txt");
                archive.ExtractToDirectory(extractPath);
            } 
        }
    }
}
```

**5､linux中调用zip和unzip命令实现**

使用.Net Core后台代码中调用linux中zip和unzip实例.zip文件压缩和解压

```c#
using System;
using System.Diagnostics;
    public static class ShellHelper
    {
        public static string Bash(this string cmd)
        {
            var escapedArgs = cmd.Replace("\"", "\\\"");
            
            var process = new Process()
            {
                StartInfo = new ProcessStartInfo
                {
                    FileName = "/bin/bash",
                    Arguments = $"-c \"{escapedArgs}\"",
                    RedirectStandardOutput = true,
                    UseShellExecute = false,
                    CreateNoWindow = true,
                }
            };
            process.Start();
            string result = process.StandardOutput.ReadToEnd();
            process.WaitForExit();
            return result;
        }
    }
```

调用执行命令：

```
ShellHelper.Bash("zip myfile.zip filename.txt");// 压缩命令：zip myfile.zip filename.txt 解压命令：unzip file.zip
```

或者



```c#
string command = "zip myfile.zip filename.txt";// 压缩命令：zip myfile.zip filename.txt 解压命令：unzip file.zip
string result = "";
using (System.Diagnostics.Process proc = new System.Diagnostics.Process())
{
    proc.StartInfo.FileName = "/bin/bash";
    proc.StartInfo.Arguments = "-c \" " + command + " \"";
    proc.StartInfo.UseShellExecute = false;
    proc.StartInfo.RedirectStandardOutput = true;
    proc.StartInfo.RedirectStandardError = true;
    proc.Start();
    result += proc.StandardOutput.ReadToEnd();
    result += proc.StandardError.ReadToEnd();
    proc.WaitForExit();
}
return result;
```



 **备注：**



处理zip存档及其文件条目的方法分布在三个类中：[ZipFile](https://docs.microsoft.com/en-us/dotnet/api/system.io.compression.zipfile?view=netframework-4.8)，[ZipArchive](https://docs.microsoft.com/en-us/dotnet/api/system.io.compression.ziparchive?view=netframework-4.8)和[ZipArchiveEntry](https://docs.microsoft.com/en-us/dotnet/api/system.io.compression.ziparchiveentry?view=netframework-4.8)。

[**ZipFile.CreateFromDirectory**](https://docs.microsoft.com/en-us/dotnet/api/system.io.compression.zipfile.createfromdirectory?view=netframework-4.8)**：**从目录创建zip存档。
[**ZipFile.ExtractToDirectory**](https://docs.microsoft.com/en-us/dotnet/api/system.io.compression.zipfile.extracttodirectory?view=netframework-4.8)**：**将zip存档的内容提取到目录中。
[**ZipArchive.CreateEntry**](https://docs.microsoft.com/en-us/dotnet/api/system.io.compression.ziparchive.createentry?view=netframework-4.8)**：**将新文件添加到现有的zip存档中。
[**ZipArchive.GetEntry**](https://docs.microsoft.com/en-us/dotnet/api/system.io.compression.ziparchive.getentry?view=netframework-4.8)**：**从zip存档中检索文件。
[**ZipArchive.Entries**](https://docs.microsoft.com/en-us/dotnet/api/system.io.compression.ziparchive.entries?view=netframework-4.8)**：**从zip存档中检索所有文件。
[**ZipArchiveEntry.Open**](https://docs.microsoft.com/en-us/dotnet/api/system.io.compression.ziparchiveentry.open?view=netframework-4.8)**：**将流打开到zip存档中包含的单个文件。
[**ZipArchiveEntry.Delete**](https://docs.microsoft.com/en-us/dotnet/api/system.io.compression.ziparchiveentry.delete?view=netframework-4.8)**：**从zip存档中删除文件。

**相关文档：**[.NET Core(C#)使用sharpcompress压缩解压文件(.rar,.zip,tar.bz2,.7z,.tar.gz)](https://www.cjavapy.com/article/343/)