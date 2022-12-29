## C# 匿名方法

C# 匿名方法是一个“内联”语句或表达式，可在需要委托类型的任何地方使用。 可以使用匿名函数来初始化命名委托，或传递命名委托作为方法参数。本文主要介绍C# 匿名方法。

## 1、C# 匿名方法的声明

可以使用 lambda 表达式或匿名方法来创建匿名函数。 建议使用 lambda 表达式，因为它们提供了更简洁和富有表现力的方式来编写内联代码。 与匿名方法不同，某些类型的 lambda 表达式可以转换为表达式树类型。匿名方法定义语法如下，

1）使用delegate 关键字创建委托实例来声明

```c#
delegate void MyDelegate(string s);
...
MyDelegate m = delegate(string x)
{
    Console.WriteLine("匿名方法: {0}", x);
};
```

2)使用 lambda 表达式

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Linq.Expressions;
using System.Text;
using System.Threading.Tasks;
namespace cjavapy
{
    delegate void MyDelegate(string s);
    class Program
    {
        static void Main(string[] args)
        {
            //Expression<del> myET = x => x * x;
            MyDelegate m =  x=>Console.WriteLine("匿名方法: {0}", x);
            Console.ReadKey();
        }
    }
}
```



## 2、匿名方法的使用

在 C# 1.0 中，通过使用在代码中其他位置定义的方法显式初始化委托来创建委托的实例。 C# 2.0 引入了匿名方法的概念，作为一种编写可在委托调用中执行的未命名内联语句块的方式。 C# 3.0 引入了 lambda 表达式，这种表达式与匿名方法的概念类似，但更具表现力并且更简练。 这两个功能统称为匿名函数。 通常，面向 .NET Framework 3.5 或更高版本的应用程序应使用 lambda 表达式。

**例如，**

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Linq.Expressions;
using System.Text;
using System.Threading.Tasks;
namespace cjavapy
{
    class Test
    {
        delegate void MyDelegate(string s);
        static void MyMethod(string s)
        {
            Console.WriteLine(s);
        }
        static void Main(string[] args)
        {
            //原始委托语法
            //使用指定方法初始化。
            MyDelegate md1 = new MyDelegate(MyMethod);
            // c# 2.0:一个委托可以被初始化
            //内部代码，称为“匿名方法”。这
            //方法以字符串作为输入参数。
            MyDelegate md2 = delegate (string s) { Console.WriteLine(s); };
            // c# 3.0。委托可以被初始化
            //一个lambda表达式lambda也接受一个字符串
            //作为输入参数(x)。x的类型由编译器推断。
            MyDelegate md3 = (x) => { Console.WriteLine(x); };
            // 调用委托。
            md1("C#");
            md2("Java");
            md3("Python");
            Console.ReadKey();
        }
    }
}
```

**相关文档：**[.NET(C#) 匿名类](https://www.cjavapy.com/article/2481/)