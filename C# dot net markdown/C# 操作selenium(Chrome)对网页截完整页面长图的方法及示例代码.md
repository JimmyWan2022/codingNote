## .NET Core(C#) 操作selenium(Chrome)对网页截完整页面长图的方法及示例代码

本文主要介绍.NET Core(C#)中，使用selenium(Chrome)载取有滚动条的页面，载取网页完整长图的方法，以及实现示例代码。

**1､使用Nuget安装引用Selenium**

安装`Selenium.Support`和`Selenium.WebDriver.ChromeDriver`

1）使用Nuget界面管理器

**相关文档**：[VS(Visual Studio)中Nuget的使用](https://www.cjavapy.com/article/21/)

2）使用Package Manager命令安装

```
PM> Install-Package Selenium.Support -Version 3.141.0
PM> Install-Package Selenium.Chrome.WebDriver -Version 79.0.0
```

3）使用.NET CLI命令安装

```
> dotnet add package Selenium.Support -Version 3.141.0
> dotnet add package Selenium.Chrome.WebDriver --version 79.0.0
```

**2､载取可视区域图片**

```c#
  public static void PageScreenshot(string url,string path)
        {
            ChromeDriver driver = null;
            try
            {
                ChromeOptions options = new ChromeOptions();
                options.AddArguments("headless", "disable-gpu");
                 driver = new ChromeDriver(Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location), options);
                driver.Navigate().GoToUrl(url);
                var screenshot = (driver as ITakesScreenshot).GetScreenshot();
                screenshot.SaveAsFile(path);
            }
            catch (Exception ex)
            {
                logger.Error(ex.Message+Environment.NewLine+ex.StackTrace);
            }
            finally
            {
                if (driver != null)
                {
                    driver.Close();
                    driver.Quit();
                }
            }
        }
```

**3､载取有滚动条网页长图**

```c#
        public static void PageScreenshot(string url,string path)
        {
            ChromeDriver driver = null;
            try
            {
                ChromeOptions options = new ChromeOptions();
                options.AddArguments("headless", "disable-gpu");
                driver = new ChromeDriver(Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location), options);
                driver.Navigate().GoToUrl(url);
                string width = driver.ExecuteScript("return document.body.scrollWidth").ToString();
                string height = driver.ExecuteScript("return document.body.scrollHeight").ToString();
                driver.Manage().Window.Size = new System.Drawing.Size(int.Parse(width), int.Parse(height)); //=int.Parse( height);
                var screenshot = (driver as ITakesScreenshot).GetScreenshot();
                screenshot.SaveAsFile(path);
            }
            catch (Exception ex)
            {
                logger.Error(ex.Message+Environment.NewLine+ex.StackTrace);
            }
            finally
            {
                if (driver != null)
                {
                    driver.Close();
                    driver.Quit();
                }
            }
        }
```