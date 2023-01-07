## C# 类class 方法

C# 是面向对象的编程语言，对象就是面向对象程序设计的核心。所谓对象就是真实世界中的实体，对象与实体是一一对应的，也就是说现实世界中每一个实体都是一个对象，它是一种具体的概念。本文主要介绍C# 类class 方法。

## 1、C# 类 方法

方法是在类中声明的，并且它们用于执行某些操作：

**相关文档：**

[C# 方法](https://www.cjavapy.com/article/1864/)

[C# 方法 参数](https://www.cjavapy.com/article/1865/)

[C# 方法 重载](https://www.cjavapy.com/article/1866/)

**例如：**

`MyClass`类中创建一个`MyMethod()`方法：

```c#
using System; 
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
namespace ConsoleApplication
{
    public class MyClass 
    {
       static void MyMethod()
       {
         Console.WriteLine("Hello World!");
       }
    }
    class Program
    {
        static void Main(string[] args)
        {
          Console.WriteLine("Hello World!");
        }
    }
}
```





当调用时，`MyMethod()`输出信息。 要调用方法，需要写方法名称，后跟两个括号()和一个分号;

**例如：**

在`Main`方法内部，调用`MyMethod()`：

```c#
using System; 
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
namespace ConsoleApplication
{
    public class MyClass 
    {
      public static void MyMethod()
      {
         Console.WriteLine("Hello World!");
      }
      public static void Main(string[] args)
      {
         MyMethod();
      }
    }
}// 输出 "Hello World!"
```







## 2、静态方法(static)和非静态方法

经常会看到具有`static`或`public`属性和方法的C# 程序。

在上面的示例中，我们创建了一个`static`方法，这意味着无需创建该类的对象即可对其进行访问，这与`public`只能由对象访问的方法不同。 

1) 静态方法不需要类实例化就可以调用，反之非静态方法需要实例化后才能调用；　

2) 静态方法只能访问静态成员和方法，非静态方法都可以访问；

3) 静态方法不能标记为`override`，导致派生类不能重写，但是可以访问；　　

4) 静态成员是在第一次使用时进行初始化。非静态的成员是在创建对象的时候，从内存分配上来说静态是连续的，非静态在内存的存储上是离散的，因此静态方法和非静态方法，在调用速度上，静态方法速度一定会快点，因为非静态方法需要实例化，分配内存，但静态方法不用，但是这种速度上差异可以忽略不计

**例如：**

下面示例`static`和`public`方法之间的区别：

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
namespace ConsoleApplication
{
    public class MyClass
    {
        // Static 方法
        static void MyStaticMethod()
        {
            Console.WriteLine("可以在不创建对象的情况下调用静态方法");
        }
        // Public 方法
        public void MyPublicMethod()
        {
            Console.WriteLine("公共方法必须通过创建对象来调用");
        }
        // Main 方法
        public static void Main(string[] args)
        {
            MyStaticMethod(); // 调用静态方法
                              // myPublicMethod(); 这个编译会报错
            MyClass myObj = new MyClass(); // 创建一个MyClass的对象
            myObj.MyPublicMethod(); // 调用对象的公共方法
        }
    }
}
```

## 3、访问对象的方法

**例如：**

创建一个`MyClass`类`my`对象。在my对象上调用`say()`和`Eat()`方法：

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
namespace ConsoleApplication
{
    // 定义MyClass类
    public class MyClass
    {
        // 定义say方法
        public void Say()
        {
            Console.WriteLine("hello,cjavapy!");
        }
        // 定义带参数的eat方法
        public void Eat(string things)
        {
            Console.WriteLine("吃的东西是: " + things);
        }
        // 在main方法, 调用myCar对象方法
        public static void Main(string[] args)
        {
            MyClass my = new MyClass();   // 创建my对象
            my.Say();      // 调用say()方法
            my.Eat("banana");          // 调用eat()方法
        }
    }
}
```

## 4、使用多个类

创建一个类的对象并在另一个类中访问它是一个好习惯。在此示例中，我们创建了两个类：

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
namespace ConsoleApplication
{
    //MainClass
    public class MainClass
    {
        public static void Main(string[] args)
        {
            MyClass myClass = new MyClass();     // 创建myClass对象
            myClass.Say();                       // 调用Say()方法
            myClass.Eat("香蕉");               // 调用Eat()方法
        }
    }
    //MyClass
    public class MyClass
    {
        // 定义Say方法
        public void Say()
        {
            Console.WriteLine("hello,cjavapy!");
        }
        // 定义带参数的Eat方法
        public void Eat(string things)
        {
            Console.WriteLine("吃的东西是: " + things);
        }
    }
}
```