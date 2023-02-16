教程Apache log4net

官网教程：https://logging.apache.org/log4net/release/manual/configuration.html

### 1.在AssemblyInfo.cs文件中加入

```
[assembly: log4net.Config.XmlConfigurator(ConfigFileExtension = "log4net", Watch = true)]
```

### 2.新增文件log4net.config

官网版本：

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <configSections>
    <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
  </configSections>
  <log4net>
    <appender name="ConsoleAppender" type="log4net.Appender.ConsoleAppender" >
      <layout type="log4net.Layout.PatternLayout">
        <conversionPattern value="%date [%thread] %-5level %logger [%ndc] - %message%newline" />
      </layout>
    </appender>
    <root>
      <level value="INFO" />
      <appender-ref ref="ConsoleAppender" />
    </root>
  </log4net>
</configuration>
```

网友版本

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <!--添加配置节点-->
    <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
  </configSections>

  <log4net outdate_days="2">
    <!--我的日志类-->
    <logger name="mylogger">
      <level value="DEBUG"/>
      <appender-ref ref="myloggerAppender"/>
    </logger>

    <!--日志保存到文件里面 txt-->
    <appender name="myloggerAppender" type="log4net.Appender.RollingFileAppender">
      <!--日志路径 网站根目录下面的logs-->
      <param name="File" value="logs\\"/>
      <!--是否是向文件中追加日志-->
      <param name="AppendToFile" value="true"/>
      <!--每天记录的日志文件个数，与maximumFileSize配合使用-->
      <param name="MaxSizeRollBackups" value="10"/>
      <param name="MaximumFileSize" value="10MB"/>
      <!--日志文件名是否是固定不变的-->
      <param name="StaticLogFileName" value="false"/>
      <param name="DatePattern" value="yyyy-MM-dd&quot;.log&quot;"/>
      <!--日志根据日期滚动-->
      <param name="RollingStyle" value="Date"/>
      <!--输出样式设置-->
      <layout type="log4net.Layout.PatternLayout">
        <param name="ConversionPattern" value="%d Thread[%t]-->%p-> %m%n"/>
      </layout>
    </appender>

  </log4net>

</configuration>
```



### 3.使用

官网使用案例：

using Com.Foo;

// Import log4net classes.
using log4net;
using log4net.Config;

public class **MyApp** 
{
    // Define a static logger variable so that it references the
    // Logger instance named "MyApp".
    **private static readonly ILog log = LogManager.GetLogger(typeof(MyApp));**

static void Main(string[] args) 
{
    // Set up a simple configuration that logs on the console.
    **BasicConfigurator.Configure();**

**log.Info("Entering application.");**
Bar bar = new Bar();
bar.DoIt();
log.Info("Exiting application.");

}

}

我的使用案例：

**黑色加粗的部分就是需要添加的内容**



输出样式设置
%m(message)	输出的日志消息，如ILog.Debug(…)输出的一条消息 
%n(new line)	换行 
%d(datetime)	输出当前语句运行的时刻 
%r(run time)	输出程序从运行到执行到当前语句时消耗的毫秒数 
%t(thread id)	当前语句所在的线程ID 
%p(priority)	日志的当前优先级别，即DEBUG、INFO、WARN…等 
%c(class)	当前日志对象的名称
%f(file)	输出语句所在的文件名。 
%l(line)	输出语句所在的文件名及行号。 
%数字	表示该项的最小长度，如果不够，则用空格填充，如“%-5level”表示level的最小宽度是5个字符，如果实际长度不够5个字符则以空格填充。

```xml
<!-- Level的级别，由高到低 --> 

<!-- None > Fatal > ERROR > WARN > DEBUG > INFO > ALL-->

<!-- 解释：如果level是ERROR，则在cs文件里面调用log4net的info()方法，则不会写入到日志文件中-->


```



```
class Program
    {
        private static readonly log4net.ILog log = log4net.LogManager.GetLogger(System.Reflection.MethodBase.GetCurrentMethod().DeclaringType);

        static void Main(string[] args)
        {
            log.Info("Hello logging world!");
            Console.WriteLine("Hit enter");
            Console.ReadLine();
        }
    }
```

