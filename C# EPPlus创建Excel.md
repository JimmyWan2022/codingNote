## .NET Core(C#) EPPlus创建Excel(.xlsx)写入数据的方法及示例代码

EPPlus是一个使用Open Office XML（Xlsx）文件格式，能读写Excel(.xlsx)文件的开源组件。本文主要介绍.NET Core(C#)中使用EPPlus创建Excel(.xlsx)写入数据的方法，及相关的示例代码。



## 1、安装引用EPPlus

1）使用Nuget界面管理器

搜索 "EPPlus" 在列表中分别找到它，点击 "安装"

**相关文档**：[VS(Visual Studio)中Nuget的使用](https://www.cjavapy.com/article/21/)

2）使用Package Manager命令安装

```
PM> Install-Package EPPlus
```

3）使用.NET CLI命令安装

```
> dotnet add package EPPlus
```

## 2、创建Excel(.xlsx)文件并写入数据

文件不存在，通过程序新建文件Excel(.xlsx)文件，并写入数据。

```c#
using OfficeOpenXml;
using OfficeOpenXml.Drawing;
using OfficeOpenXml.Style;
using System;
using System.Drawing;
using System.IO;
namespace ConsoleApp1
{
    class Program
    {
        static void Main(string[] args)
        {
            var filename = "demo.xlsx";
            ExcelPackage.LicenseContext = LicenseContext.NonCommercial;
            using (ExcelPackage package = new ExcelPackage(new FileInfo(filename)))
            {
                var worksheet = package.Workbook.Worksheets.Add("Sheet1");
                worksheet.SetValue($"B3", "AAA");// 设置值
                worksheet.InsertRow(6, 2, 6); // 插入行， 参数1：在第几行插入，参数2：插入几行，参数3：从第几行赋值格式
                worksheet.Cells[$"B2:D2"].Merge = true; // 合并从B2到D2的单元格
                var logo = Image.FromFile($@"{Environment.CurrentDirectory}/1.jpg");  // 读取图片
                var picture = worksheet.Drawings.AddPicture("logo", logo); // 添加图片
                picture.SetSize(130, 32);  // 设置图片大小
                picture.SetPosition(1, 3, 1, 3); // 插入图片，参数1：插入的行，参数2：行偏移量，参数3：插入的列，参数4：列偏移量
                worksheet.Cells.Style.ShrinkToFit = true;//单元格自动适应大小
                worksheet.Row(1).Height = 15;//设置行高
                worksheet.Row(1).CustomHeight = true;//自动调整行高
                worksheet.Column(1).Width = 15;//设置列宽
                worksheet.Cells.Style.WrapText = true;//自动换行
                worksheet.Cells[1, 1].Style.Font.Bold = true;//字体为粗体
                worksheet.Cells[1, 1].Style.Font.Color.SetColor(Color.White);//字体颜色
                worksheet.Cells[1, 1].Style.Font.Name = "微软雅黑";//字体
                worksheet.Cells[1, 1].Style.Font.Size = 12;//字体大小
                worksheet.Cells[5, 3].Style.Numberformat.Format = "#,##0.00";//这是保留两位小数
                worksheet.Cells["D2:D5"].Formula = "B2*C2";//这是乘法的公式，意思是第二列乘以第三列的值赋值给第四列，这种方法比较简单明了
                worksheet.Cells[6, 2, 6, 4].Formula = string.Format("SUBTOTAL(9,{0})", new ExcelAddress(2, 2, 5, 2).Address);//这是自动求和的方法，至于subtotal的用法你需要自己去了解了
                worksheet.Cells[1, 1].Style.Fill.PatternType = ExcelFillStyle.Solid;
                worksheet.Cells[1, 1].Style.Fill.BackgroundColor.SetColor(Color.FromArgb(128, 128, 128));//设置单元格背景色
                ExcelPicture picLink = worksheet.Drawings.AddPicture("logo1", Image.FromFile(@"firstbg.jpg"), new ExcelHyperLink("https:\\www.cjavapy.com", UriKind.Relative)); //图片超链接
                picLink.SetPosition(15, 3, 2, 3);
                picLink.SetSize(493, 211);
                worksheet.Cells[1, 1].Hyperlink = new ExcelHyperLink("https:\\www.cjavapy.com", UriKind.Relative); //单元格超链接
                worksheet.Cells[1, 1].Value = "date";
                worksheet.Cells[1, 2].Value = "price";
                worksheet.Cells[1, 3].Value = "volume";
                var random = new Random();
                for (int i = 0; i < 10; i++)
                {
                    worksheet.Cells[i + 2, 1].Value = DateTime.Today.AddDays(i);
                    worksheet.Cells[i + 2, 2].Value = random.NextDouble() * 1e3;
                    worksheet.Cells[i + 2, 3].Value = random.Next() / 1e3;
                }
                worksheet.Cells[2, 1, 11, 1].Style.Numberformat.Format = "dd/MM/yyyy";
                worksheet.Cells[2, 2, 11, 2].Style.Numberformat.Format = "#,##0.111111";
                worksheet.Cells[2, 3, 11, 3].Style.Numberformat.Format = "#,##0";
                worksheet.Column(1).AutoFit();
                worksheet.Column(2).AutoFit();
                worksheet.Column(3).AutoFit();
                package.Save();
            }
        }
    }
}
```

