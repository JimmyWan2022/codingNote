## .NET(C#) Nullable(可空类型)通过扩展方法传委托参数调用方法



本文主要介绍可空类型(Nullable<T>)的使用和通过扩展方法传委托参数调用方法，实现可空类型为null的情况时调用方法的判断。



**1、可空类型介绍及使用**

正常情况下，值类型是不为`null`，必须有值，而可空类型可以理解成一种特殊的值类型，可以为`null`，比如，`int? a=1;a=null;`中`a`变量是int类型并且可以为`null`。常用的值类型：byte，short，int，long，float，double，decimal，char，bool，enum 和struct。 

可空类型的使用示例代码如下：

```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
namespace ConsoleApplication1
{
	class Program
	{
		static void Main(string[] args)
		{
			int? i = null;
			if (!i.HasValue) // 不为null,则 i.HasValue 为true
			{
				i = 99;
			}
			Console.WriteLine(i.Value); // i 的值
		}
	}
}
// i.HasValue 比 i != null 复杂一点，i.Value 也比 i 更麻烦
// 但是当使用更加复杂的值类型（struct）来声明可空类型时， .HasValue 和 .Value 就有了优势。
```





**2、可空类型扩展方法传委托参数调用方法**

实现可空类型为`null`的情况的判断，如果x不为空，它将调用函数`Foo`。它将把结果包装回一个`Nullable<R>`。如果`x`为`null`，它将返回带`null`的`Nullable<R>`。

```c#
public int Foo(int a)
{
    // ...
}
// in some other method
int? x = 0;
x = Foo(x);public static class FuncUtils {    public static Nullable<R> Fmap<T, R>(this Nullable<T> x, Func<T, R> f)        where T : struct        where R : struct {        if(x != null) {            return f(x.Value);        } else {            return null;        }    }}
```

调用方法如下：

```c#
int? x = 0;
x = x.Fmap(Foo);
```

或者可以写一个更等价的函数(如Haskell中的`fmap`)，其中我们有一个函数`fmap`，它接受`Func<T, R>`作为输入，返回一个`Func<Nullable<T>， Nullable<R>>`，这样我们就可以对特定的`x`使用它:

```c#
public static class FuncUtils {
    public static Func<Nullable<T>, Nullable<R>> Fmap<T, R>(Func<T, R> f)
        where T : struct
        where R : struct {
        return delegate (Nullable<T> x) {
            if(x != null) {
                return f(x.Value);
            } else {
                return null;
            }
        };
    }
}
```

调用方法如下：

```c#
var fmapf = FuncUtils.Fmap<int, int>(Foo);
fmapf(null);  // -> null
fmapf(12);    // -> Foo(12) as int?
```