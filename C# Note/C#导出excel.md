## EXCEL导出，使用教程

1.eppluss，需要下载老版本（包括5.0版本 and lower version）

https://www.epplussoftware.com/

2.在.NET项目项目中下载，NuGet包管理器中下载相应的版本

![](C#导出excel.assets/image-20221006111550112.png)



3.在using的部分选择商业使用，还是非商业（低版本的包是开源的，不受影响）

using LicenseContext = OfficeOpenXml.LicenseContext;

----------------

## CODE

```c#
public void writeExcel(string str1,Boolean boolValue) {
            DateTime dataTime = DateTime.Now;
            //string mydate = string.Format("{0:D4}{1:D2}{2:D2}", dataTime.Year, dataTime.Month, dataTime.Day);
            string mydate = string.Format("{0:D4}年{1:D2}月{2:D2}日", dataTime.Year, dataTime.Month, dataTime.Day);
            string str2;
            ExcelPackage.LicenseContext = LicenseContext.NonCommercial;
            if (boolValue)
            {
                //true
                 str2 = "老练";
            }
            else {//false
                str2 = "温循";
            }
            string excelName = str2 + " " + mydate + "产品" + str1 + ".xlsx";

            deleteOneFile(excelName);
            //deleteOneFile(truePath);
            using (var package = new ExcelPackage(new FileInfo(excelName)))
            //using (var package = new ExcelPackage())
            {
                //var worksheet = package.Workbook.Worksheets.Add("Inventory");

                //package.Workbook.Worksheets.Delete("Sheet1");
                var worksheet = package.Workbook.Worksheets.Add("Sheet1");
                //Add the headers
                //sheet.Cells[fromRow, fromCol, toRow, toCol].Merge = true;
                //sheet.Cells[fromRow, fromCol, toRow, toCol].Style.VerticalAlignment = ExcelVerticalAlignment.Center;

                worksheet.Cells[5, 2].Value = "产品编号：";
                //worksheet.Cells[5, 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);
                worksheet.Cells[5, 4].Value = "日期：";
                //worksheet.Cells[5, 4].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);
                worksheet.Cells[5, 6].Value = "测试人员：";
                //worksheet.Cells[5, 6].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[8, 2, 9, 2].Merge = true;
                worksheet.Cells[8, 2, 9, 2].Value = "测试周期";
                worksheet.Cells[8, 2, 9, 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);
                worksheet.Cells[8, 3, 9, 3].Merge = true;
                worksheet.Cells[8, 3, 9, 3].Value = "测试具体时间";
                worksheet.Cells[8, 3, 9, 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);
                //worksheet.Cells[8, 3, 9, 3].AutoFitColumns();


                worksheet.Cells[8, 4, 9, 4].Merge = true;
                worksheet.Cells[8, 4, 9, 4].Value = "测试项目";
                worksheet.Cells[8, 4, 9, 4].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);
                worksheet.Cells[8, 5, 9, 5].Merge = true;
                worksheet.Cells[8, 5, 9, 5].Value = "要求值/状态";
                worksheet.Cells[8, 5, 9, 5].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);
                worksheet.Cells[8, 6, 9, 6].Merge = true;
                worksheet.Cells[8, 6, 9, 6].Value = "实测值";
                worksheet.Cells[8, 6, 9, 6].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);
                worksheet.Cells[8, 7, 9, 7].Merge = true;
                worksheet.Cells[8, 7, 9, 7].Value = "结论";
                worksheet.Cells[8, 7, 9, 7].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);
                int startRowOld = 10;
                int startColOld = 2;
                int startCol;
                int startRow;
                //老练前
                startRow = startRowOld ;
                startCol = startColOld;
                worksheet.Cells[startRow, startCol, startRow + 5, startCol].Merge = true;
                worksheet.Cells[startRow, startCol, startRow + 5, startCol].Value =str2+ "前";
                worksheet.Cells[startRow, startCol, startRow + 5, startCol].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow, startCol, startRow + 5, startCol].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Merge = true;
                worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Value = DateTime.Now.ToString("G");
                worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow, startCol + 2, startRow, startCol + 2].Value = "常值工作电流";
                worksheet.Cells[startRow, startCol + 2, startRow, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow, startCol + 2, startRow, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 1, startCol + 2, startRow + 1, startCol + 2].Value = "脉冲工作电流";
                worksheet.Cells[startRow + 1, startCol + 2, startRow + 1, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 1, startCol + 2, startRow + 1, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 2, startCol + 2, startRow + 2, startCol + 2].Value = "自检功能测试";
                worksheet.Cells[startRow + 2, startCol + 2, startRow + 2, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 2, startCol + 2, startRow + 2, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 3, startCol + 2, startRow + 3, startCol + 2].Value = "脱落反馈功能测试";
                worksheet.Cells[startRow + 3, startCol + 2, startRow + 3, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 3, startCol + 2, startRow + 3, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 4, startCol + 2, startRow + 4, startCol + 2].Value = "全路测试";
                worksheet.Cells[startRow + 4, startCol + 2, startRow + 4, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 4, startCol + 2, startRow + 4, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 5, startCol + 2, startRow + 5, startCol + 2].Value = "逐路测试";
                worksheet.Cells[startRow + 5, startCol + 2, startRow + 5, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 5, startCol + 2, startRow + 5, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                //

                worksheet.Cells[startRow, startCol + 3, startRow, startCol + 3].Value = "≤0.5A";
                worksheet.Cells[startRow, startCol + 3, startRow, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow, startCol + 3, startRow, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 1, startCol + 3, startRow + 1, startCol + 3].Value = "≤5A";
                worksheet.Cells[startRow + 1, startCol + 3, startRow + 1, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 1, startCol + 3, startRow + 1, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 2, startCol + 3, startRow + 2, startCol + 3].Value = "正常";
                worksheet.Cells[startRow + 2, startCol + 3, startRow + 2, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 2, startCol + 3, startRow + 2, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 3, startCol + 3, startRow + 3, startCol + 3].Value = "正常";
                worksheet.Cells[startRow + 3, startCol + 3, startRow + 3, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 3, startCol + 3, startRow + 3, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 4, startCol + 3, startRow + 4, startCol + 3].Value = "正常";
                worksheet.Cells[startRow + 4, startCol + 3, startRow + 4, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 4, startCol + 3, startRow + 4, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 5, startCol + 3, startRow + 5, startCol + 3].Value = "正常";
                worksheet.Cells[startRow + 5, startCol + 3, startRow + 5, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 5, startCol + 3, startRow + 5, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);


                for (int i = 1; i <= 24; i++)
                {
                    startRow = startRowOld + i * 4+6;
                    startCol = startColOld;
                    worksheet.Cells[startRow, startCol, startRow + 5, startCol].Merge = true;
                    worksheet.Cells[startRow, startCol, startRow + 5, startCol].Value = "第" + i + "个循环";
                    worksheet.Cells[startRow, startCol, startRow + 5, startCol].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                    worksheet.Cells[startRow, startCol, startRow + 5, startCol].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                    worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Merge = true;
                    //worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Value = "xx.xx.xx";
                    worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Value = DateTime.Now.ToString("G");
                    worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                    worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                    worksheet.Cells[startRow, startCol + 2, startRow, startCol + 2].Value = "常值工作电流";
                    worksheet.Cells[startRow, startCol + 2, startRow, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                    worksheet.Cells[startRow, startCol + 2, startRow, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                    worksheet.Cells[startRow + 1, startCol + 2, startRow + 1, startCol + 2].Value = "脉冲工作电流";
                    worksheet.Cells[startRow + 1, startCol + 2, startRow + 1, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                    worksheet.Cells[startRow + 1, startCol + 2, startRow + 1, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                    worksheet.Cells[startRow + 2, startCol + 2, startRow + 2, startCol + 2].Value = "自检功能测试";
                    worksheet.Cells[startRow + 2, startCol + 2, startRow + 2, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                    worksheet.Cells[startRow + 2, startCol + 2, startRow + 2, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                    worksheet.Cells[startRow + 3, startCol + 2, startRow + 3, startCol + 2].Value = "脱落反馈功能测试";
                    worksheet.Cells[startRow + 3, startCol + 2, startRow + 3, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                    worksheet.Cells[startRow + 3, startCol + 2, startRow + 3, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                    //

                    worksheet.Cells[startRow, startCol + 3, startRow, startCol + 3].Value = "≤0.5A";
                    worksheet.Cells[startRow, startCol + 3, startRow, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                    worksheet.Cells[startRow, startCol + 3, startRow, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                    worksheet.Cells[startRow + 1, startCol + 3, startRow + 1, startCol + 3].Value = "≤5A";
                    worksheet.Cells[startRow + 1, startCol + 3, startRow + 1, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                    worksheet.Cells[startRow + 1, startCol + 3, startRow + 1, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                    worksheet.Cells[startRow + 2, startCol + 3, startRow + 2, startCol + 3].Value = "正常";
                    worksheet.Cells[startRow + 2, startCol + 3, startRow + 2, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                    worksheet.Cells[startRow + 2, startCol + 3, startRow + 2, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                    worksheet.Cells[startRow + 3, startCol + 3, startRow + 3, startCol + 3].Value = "正常";
                    worksheet.Cells[startRow + 3, startCol + 3, startRow + 3, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                    worksheet.Cells[startRow + 3, startCol + 3, startRow + 3, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                }
                //老练后
                startRow = startRowOld + 4*24+6;
                startCol = startColOld;
                worksheet.Cells[startRow, startCol, startRow + 5, startCol].Merge = true;
                worksheet.Cells[startRow, startCol, startRow + 5, startCol].Value = str2 + "后";
                worksheet.Cells[startRow, startCol, startRow + 5, startCol].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow, startCol, startRow + 5, startCol].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Merge = true;
                worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Value = DateTime.Now.ToString("G");
                worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow, startCol + 1, startRow + 5, startCol + 1].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow, startCol + 2, startRow, startCol + 2].Value = "常值工作电流";
                worksheet.Cells[startRow, startCol + 2, startRow, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow, startCol + 2, startRow, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 1, startCol + 2, startRow + 1, startCol + 2].Value = "脉冲工作电流";
                worksheet.Cells[startRow + 1, startCol + 2, startRow + 1, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 1, startCol + 2, startRow + 1, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 2, startCol + 2, startRow + 2, startCol + 2].Value = "自检功能测试";
                worksheet.Cells[startRow + 2, startCol + 2, startRow + 2, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 2, startCol + 2, startRow + 2, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 3, startCol + 2, startRow + 3, startCol + 2].Value = "脱落反馈功能测试";
                worksheet.Cells[startRow + 3, startCol + 2, startRow + 3, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 3, startCol + 2, startRow + 3, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 4, startCol + 2, startRow + 4, startCol + 2].Value = "全路点火测试";
                worksheet.Cells[startRow + 4, startCol + 2, startRow + 4, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 4, startCol + 2, startRow + 4, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 5, startCol + 2, startRow + 5, startCol + 2].Value = "逐路点火测试";
                worksheet.Cells[startRow + 5, startCol + 2, startRow + 5, startCol + 2].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 5, startCol + 2, startRow + 5, startCol + 2].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                //

                worksheet.Cells[startRow, startCol + 3, startRow, startCol + 3].Value = "≤0.5A";
                worksheet.Cells[startRow, startCol + 3, startRow, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow, startCol + 3, startRow, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 1, startCol + 3, startRow + 1, startCol + 3].Value = "≤5A";
                worksheet.Cells[startRow + 1, startCol + 3, startRow + 1, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 1, startCol + 3, startRow + 1, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 2, startCol + 3, startRow + 2, startCol + 3].Value = "正常";
                worksheet.Cells[startRow + 2, startCol + 3, startRow + 2, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 2, startCol + 3, startRow + 2, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 3, startCol + 3, startRow + 3, startCol + 3].Value = "正常";
                worksheet.Cells[startRow + 3, startCol + 3, startRow + 3, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 3, startCol + 3, startRow + 3, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 4, startCol + 3, startRow + 4, startCol + 3].Value = "正常";
                worksheet.Cells[startRow + 4, startCol + 3, startRow + 4, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 4, startCol + 3, startRow + 4, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);

                worksheet.Cells[startRow + 5, startCol + 3, startRow + 5, startCol + 3].Value = "正常";
                worksheet.Cells[startRow + 5, startCol + 3, startRow + 5, startCol + 3].Style.VerticalAlignment = ExcelVerticalAlignment.Center;
                worksheet.Cells[startRow + 5, startCol + 3, startRow + 5, startCol + 3].Style.Border.BorderAround(ExcelBorderStyle.Thin, Color.Black);



                worksheet.Column(2).Width = 15;
                worksheet.Column(3).Width = 20;
                worksheet.Column(4).Width = 15;
                worksheet.Column(5).Width = 15;
                //worksheet.Cells.Style.WrapText = true;
                worksheet.Cells.Style.Font.Name = "宋体";
                worksheet.Cells.Style.Font.Size = 11;

                worksheet.Cells[3, 2, 3, 7].Merge = true;
                string str3;
                if (boolValue) {
                    //true
                     str3 = "测试记录表1";

                }
                else {//false
                     str3 = "测试记录表2";
                }
                worksheet.Cells[3, 2, 3, 7].Value = str3;
                worksheet.Cells[3, 2, 3, 7].Style.Font.Size = 16;

                package.Save();
                string str11 = Directory.GetCurrentDirectory();

                Console.WriteLine(str3+" 文件创建成功");
                MessageBox.Show("文件导出成功");
                string filePath = Environment.CurrentDirectory;
                System.Diagnostics.Process.Start(filePath);

            }
        }
```

```c#
 public static void deleteOneFile(string fileFullPath)
        {
            //string path1 = Environment.CurrentDirectory + "\\MyTest";
            string path2 = Environment.CurrentDirectory + "\\" + fileFullPath;
            //string path3 = Environment.CurrentDirectory + "\\MyWorkbook.xlsx";
            //string truePath = @"C:\MyWorkbook.xlsx"; //绝对路径
            //删除文件夹
            //if (Directory.Exists(path1))
            //    Directory.Delete(path1, true);

            //删除文件
            if (File.Exists(path2))
            {
                File.Delete(path2);
                Console.WriteLine(fileFullPath + "已被删除");
                //MessageBox.Show("删除成功");
            }

            else
            {
                Console.WriteLine("删除失败,当前目录下不存在" + fileFullPath);
                //MessageBox.Show("删除失败");
            }
        }
```

