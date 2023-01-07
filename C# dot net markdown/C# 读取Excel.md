# .NET Core(C#) EPPlus读取Excel(.xlsx)文件的方法及示例代码

EPPlus是一个使用Open Office XML（Xlsx）文件格式，能读写Excel(.xlsx)文件的开源组件。本文主要介绍.NET Core(C#)中使用EPPlus读取Excel(.xlsx)文件的方法，及相关的示例代码。

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



## 2、读取Excel(.xlsx)文件

读写已经存在的Excel(.xlsx)文件的示例代码，具体如下：

```C#
public static void readXLS(string FilePath)
{
    FileInfo existingFile = new FileInfo(FilePath);
    using (ExcelPackage package = new ExcelPackage(existingFile))
    {
        //get the first worksheet in the workbook
        ExcelWorksheet worksheet = package.Workbook.Worksheets[1];
        int colCount = worksheet.Dimension.End.Column;  //get Column Count
        int rowCount = worksheet.Dimension.End.Row;     //get row count
        for (int row = 1; row <= rowCount; row++)
        {
            for (int col = 1; col <= colCount; col++)
            {
                Console.WriteLine(" Row:" + row + " column:" + col + " Value:" + worksheet.Cells[row, col].Value?.ToString().Trim());
            }
        }
    }
}
public static T ReadFromExcel<T>(string path, bool hasHeader = true)
{
    using (var excelPack = new ExcelPackage())
    {
        //Load excel stream
        using (var stream = File.OpenRead(path))
        {
            excelPack.Load(stream);
        }
        //处理第一个工作表。(如果处理多个表格，可以在此用for循环处理)
        var ws = excelPack.Workbook.Worksheets[0];
        DataTable excelasTable = new DataTable();
        foreach (var firstRowCell in ws.Cells[1, 1, 1, ws.Dimension.End.Column])
        {
            if (!string.IsNullOrEmpty(firstRowCell.Text))
            {
                string firstColumn = string.Format("Column {0}", firstRowCell.Start.Column);
                excelasTable.Columns.Add(hasHeader ? firstRowCell.Text : firstColumn);
            }
        }
        var startRow = hasHeader ? 2 : 1;
        for (int rowNum = startRow; rowNum <= ws.Dimension.End.Row; rowNum++)
        {
            var wsRow = ws.Cells[rowNum, 1, rowNum, excelasTable.Columns.Count];
            DataRow row = excelasTable.Rows.Add();
            foreach (var cell in wsRow)
            {
                row[cell.Start.Column - 1] = cell.Text;
            }
        }
       //将所有内容作为泛型获取，最终定是否强制转换为所需类型
        var generatedType = JsonConvert.DeserializeObject<T>(JsonConvert.SerializeObject(excelasTable));
        return (T)Convert.ChangeType(generatedType, typeof(T));
    }
}
```

