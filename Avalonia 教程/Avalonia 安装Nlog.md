Avalonia是一个采用C＃编写的跨平台的用户界面框架。NLog是一个用于.NET和.NET Core的现代日志记录库，支持大多数常见的日志方案。 在Avalonia中使用NLog有多种方法，以下是其中之一：

1. 首先，在项目中安装NLog的NuGet包：

   `dotnet add package NLog`

2. 然后，在App.xaml.cs文件中设置NLog：

   ```
   Copy codeusing NLog;
   using NLog.Targets;
   
   public class App : Application
   {
       static App()
       {
           SetupLogging();
       }
   
       private static void SetupLogging()
       {
           // Create logging configuration
           var config = new NLog.Config.LoggingConfiguration();
   
           // Create targets and add them to the configuration
           var fileTarget = new FileTarget("fileTarget")
           {
               FileName = "${basedir}/logs/${shortdate}.log",
               Layout = "${longdate} ${level} ${message}"
           };
           config.AddTarget(fileTarget);
   
           // Set rules and add them to the configuration
           var rule = new NLog.Config.LoggingRule("*", LogLevel.Debug, fileTarget);
           config.LoggingRules.Add(rule);
   
           // Activate the configuration
           LogManager.Configuration = config;
       }
   
       // ...
   }
   ```

   上面的示例创建一个日志文件，在应用程序根目录下的logs文件夹中以每天的日期作为文件名。还定义了一个规则，它指定记录所有的日志级别。

3. 最后，在需要记录日志的文件中使用 LogManager 对象来记录日志：

   ```
   Copy codeusing NLog;
   
   public class MyClass
   {
       private static readonly Logger logger = LogManager.GetCurrentClassLogger();
   
       public void MyMethod()
       {
           // ...
           logger.Info("Some information");
           // ...
       }
   }
   ```

上面的代码片段将记录一个信息级别的日志，使用当前类的名称作为记录器名称。

这是使用NLog在Avalonia中进行日志记录的一种简单方法。你可以根据需要进行更改，例如定义不同的日志文件或用不同的日志级别记录不同的类。