# .NET(C#)使用委托(delegate)和Func<in T, out TResult>作为方法返回值

- [levi](javascript:void(0);) 编辑于 2020-03-15

本文主要介绍.NET(C#)中，使用委托(delegate)类型作为方法返回值类型，且直接返回Func<in T, out TResult>的问题，以及问题示例代码。

**1､委托(delegate)和Func<in T, out TResult>**

**Func<in T, out TResult>**：.NET框架中使用 `delegate`定义的泛型委托。类似还有`Action` , `Predicate` ，NET框架中源码如下：

```
public delegate void Action<in T>(T obj);
public delegate TResult Func<in T, out TResult>(T arg);
public delegate bool Predicate<in T>(T obj);
```

**委托(delegate)**：使用delegate关键字来自定义委托。

```
public delegate void MyDelegate(int x);
```

**2､使用委托(delegate)作为方法返回类型**

```
//定义委托
public delegate int callback(int i);
//能正常返回callback委托
public callback Success()
{
    Func<int, int> f = x => x;
    //return new test(f.Invoke);
    return f.Invoke; // 返回 Func<int, int>类型，需要使用f.Invoke,相当于return new callback(f.Invoke);
}
//错误写法
public callback Fail()
{
    Func<int, int> f = x => x;
    return f; // 不能直接返回，因为是两个委托，会编译错误
}
```