# .NET Core(C#)实现定时任务的三种方法(Timer、Quartz.NET、sleep和Task)

- [levi](javascript:void(0);) 编辑于 2020-07-07

本文主要介绍.NET Core(C#)中，通过timer、Quartz.NET、while和sleep实现定时执行指定任务的方法，以及相关的示例代码。



**1、使用System.Timers.Timer和System.Threading.Timer实现定时任务**

1）使用System.Timers.Timer实现

**文档：**[.NET Core(C#) System.Timers.Timer使用实现定时任务及示例代码](https://www.cjavapy.com/article/730/)

2）使用System.Threading.Timer实现

**文档：**[.NET Core(C#) System.Threading.Timer使用实现定时任务及示例代码](https://www.cjavapy.com/article/726/)

**2、使用Quartz.NET实现定时任务**

**文档：**[.NET Core(C#) Quartz.NET实现定时任务的方法及示例代码](https://www.cjavapy.com/article/736/)

**3、使用while和sleep及Task实现定时任务**

```
public class TaskTimer
{
    private static int _interval = 1000;//间隔时间
    private static bool _isRuning = false;
    private static Action _action = () =>
    {
        while (_isRuning)
        {
            taskAction();
            System.Threading.Thread.Sleep(_interval);
        }
    };
    private static Action taskAction;
    Task timer = new Task(_action);
    public TaskTimer(int interval, Action action)
    {
        _interval = interval;
        taskAction = action;
    }
    public void Start()
    {
        _isRuning = true;
        if (timer == null)
            timer = new Task(_action);
        timer.Start();
    }
    public void Stop()
    {
        _isRuning = false;
        timer.Wait();
        timer.Dispose();
        timer = null;
    }
}
```

**使用示例：**

```
TaskTimer timer = new TaskTimer(1000, () =>
            {
                Console.WriteLine(DateTime.Now);
            });
timer.Start();//启动
timer.Stop();//停止
```