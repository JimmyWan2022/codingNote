# .NET Core(C#)方法返回多个的值方法及示例代码

本文主要介绍.NET Core(C#)中，函数方法想返回多个值，通过元组、列表、数组、类、结构体和out参数实现的方法及示例代码。

**1､使用元组实现返回多个值(ValueTuple和Tuple)**

1) 使用ValueTuple实现

`ValueTuple`命名为元组（在C＃7.1中可用），优点是它最简洁，不可变且易于构造。

```c#
private (double first, double second) GetHeight()
{
   return (1,2);
}
var result = GetHeight();
Console.WriteLine($"{result.first}, {result.second}");C
var (first, second) = GetHeight();
Console.WriteLine($"{first}, {second}");
```

2) 使用Tuple实现

.NET Framework已经具有通用的`Tuple`类。但是，这些类有两个主要限制。首先，Tuple类将其属性命名为Item1，Item2，依此类推。这些名称不包含语义信息。使用这些元组类型不能实现传达每个属性的含义。新的语言功能使您可以为元组中的元素声明和使用语义上有意义的名称。

```c#
public Tuple<int, int> ViaClassicTuple()
{
   return new Tuple<int, int>(1,2);
}
var tuple = ViaClassicTuple();
Console.WriteLine($"{tuple.Item1}, {tuple.Item2}");
```

**2､使用列表(list<T>)或数组实现返回多个值**

1) 使用List列表实现

```c#
private List<double> GetHeight()
{
   return new List<double>(){1,2};
}
var result = GetHeight();
Console.WriteLine($"{result[0]}, {result[1]}");
```

2) 使用数组实现

```c#
private double[] GetHeight()
{
      return new double[2]{ 1,2};
}
var result = GetHeight();
Console.WriteLine($"{result[0]}, {result[1]}");
```

**3､使用类或结构体返回多个值**

1) 使用类实现

```c#
public class SomeClass
{
   public int First { get; set; }
   public int Second { get; set; }
   public SomeClass(int first, int second)
   {
      First = first;
      Second = second;
   }
}
public SomeClass ViaSomeClass()
{
   return new SomeClass(1, 2);
}
var someClass = ViaSomeClass();
Console.WriteLine($"{someClass.First}, {someClass.Second}");
```

2) 使用结构体实现

```c#
public struct ClassicStruct
{
   public int First { get; set; }
   public int Second { get; set; }
   public ClassicStruct(int first, int second)
   {
      First = first;
      Second = second;
   }
}
public ClassicStruct ViaClassicStruct()
{
   return new ClassicStruct(1, 2);
}
var classicStruct = ViaClassicStruct();
Console.WriteLine($"{classicStruct.First}, {classicStruct.Second}");
```

**4､使用out参数实现**

参数进行的任何操作都是在自变量上进行的。就像`ref`关键字一样，除了ref要求在传递变量之前先对其进行初始化。它也类似于in关键字，除了in不允许调用的方法修改参数值。要使用`out`参数，方法定义和调用方法都必须显式使用out关键字。

1) 多个out参数实现

```c#
public bool ViaOutParams(out int first, out int second)
{
   first = 1;
   second = 2;
   return someCondition;
}
if(ViaOutParams(out var firstInt, out var secondInt))
   Console.WriteLine($"{firstInt}, {secondInt}");
```

2) 使用out ValueTuple实现

```c#
public bool ViaOutTuple(out (int first,int second) output)
{
   output = (1, 2);
   return someCondition;
}
if (ViaOutTuple(out var output))
   Console.WriteLine($"{output.first}, {output.second}");
```