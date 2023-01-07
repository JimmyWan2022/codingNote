## C# 事件(event)

事件是一种特殊的多播委托，仅可以从声明事件的类或结构中对其进行调用。类或对象可以通过事件向其他类或对象通知发生的相关事情。 发送（或引发）事件的类称为“发布者”，接收（或处理）事件的类称为“订阅者”。本文主要介绍C# 事件(event)。

## 1、事件

事件是一种专门的代表类型，主要用于消息或通知传递。事件只能从它们发布的类型调用，通常基于`EventHandler`委托，其中对象代表事件的发送方，`System.EventArgs`派生类保存有关事件的数据。实现事件和回调方法都是基于委托（delegate）。

## 2、事件的声明



**相关文档**：[C# 委托(delegate)](https://www.cjavapy.com/article/1913/)



声明事件需要先声明该事件的委托型类，通过声明的委托类型和`event`关键字来声明事件。

## 例如，

```
public delegate void ClickEventHandler(string args);
// 基于上面的委托定义事件
public event ClickEventHandler ClickEvent;
```

以上代码简单实现了事件`ClickEvent`的定义，该事件在声明的时会调用委托。下面看一下通过“订阅者”和“发布者”类实现的完整示例：

**例如，**

```c#
using System;
namespace SimpleEvent
{
  using System;
  /***********发布器类***********/
  public class EventDemo
  {
    private string message;
    public delegate void PrintEventHandler();

    public event PrintEventHandler PrintEvent;
    protected virtual void OnPrinted()
    {
      if ( PrintEvent != null )
      {
        PrintEvent(); /* 事件被触发 */
      }else {
        Console.WriteLine( "事件没被触发" );
        Console.ReadKey(); /* 回车继续 */
      }
    }

    public EventDemo()
    {
      PrintMessage("cjavapy");
    }

    public void PrintMessage( string s )
    {
        message=s;
        OnPrinted();
    }
  }

  /***********订阅器类***********/
  public class SubscribEvent
  {
    public void Print()
    {
      Console.WriteLine( "触发事件" );
      Console.ReadKey(); /* 回车继续 */
    }
  }
  /***********触发***********/
  public class MainClass
  {
    public static void Main()
    {
      EventDemo e = new EventDemo(); /* 实例化对象,第一次没有触发事件 */
      SubscribEvent s = new SubscribEvent(); /* 实例化对象 */
      e.PrintEvent += new EventDemo.PrintEventHandler(s.Print); /* 注册 */
      e.PrintMessage("C#");
      e.PrintMessage("Java");
    }
  }
}
```

上面示例的事件没有参数，下面看一下委托事件带有参数的情况：

**例如，**

```c#
using System;
namespace SimpleEvent
{
  using System;
  /***********发布器类***********/
  public class EventDemo
  {
    private string message;
    public delegate void PrintEventHandler(string args);

    public event PrintEventHandler PrintEvent;
    protected virtual void OnPrinted(string s)
    {
      if ( PrintEvent != null )
      {
        PrintEvent(s); /* 事件被触发 */
      }else {
        Console.WriteLine( "事件没被触发" );
        Console.ReadKey(); /* 回车继续 */
      }
    }

    public EventDemo()
    {
      PrintMessage("cjavapy");
    }

    public void PrintMessage( string s )
    {
        message=s;
        OnPrinted(s);
    }
  }

  /***********订阅器类***********/
  public class SubscribEvent
  {
    public void Print(string s)
    {
      Console.WriteLine( "触发事件" );
      Console.WriteLine( s );
      Console.ReadKey(); /* 回车继续 */
    }
  }
  /***********触发***********/
  public class MainClass
  {
    public static void Main()
    {
      EventDemo e = new EventDemo(); /* 实例化对象,第一次没有触发事件 */
      SubscribEvent s = new SubscribEvent(); /* 实例化对象 */
      e.PrintEvent += new EventDemo.PrintEventHandler(s.Print); /* 注册 */
      e.PrintMessage("C#");
      e.PrintMessage("Java");
    }
  }
}
```