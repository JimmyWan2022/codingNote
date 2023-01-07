## C# 反射(Reflection)

反射提供描述程序集、模块和类型的对象（Type 类型）。 可以使用反射动态地创建类型的实例，将类型绑定到现有对象，或从现有对象中获取类型，然后调用其方法或访问器字段和属性。 如果代码中使用了特性(Attribute)，可以利用反射来访问它们。本文主要介绍C# 反射(Reflection)。

## 1、C# 反射(Reflection)使用场景

反射在以下情况下很有用：

1）需要访问程序元数据中的特性时。

2）检查和实例化程序集中的类型。

3）在运行时构建新类型。 使用 `System.Reflection.Emit` 中的类。

4）执行后期绑定，访问在运行时创建的类型上的方法。可以动态调用执行方法。

5）还可以使用反射，动态加载程序集创建对象调用方法，一般CS程序中插件一般可以这样实现。

## 2、C# 反射(Reflection)的使用

下面通过一个简单的示例，了解一下最简单的反射，使用方法 [GetType()](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.gettype#System_Object_GetType)（被 `Object` 基类的所有类型继承）以获取变量类型：

**例如，**

```
//使用GetType获取类型信息:
int i = 33;
Type type = i.GetType();
Console.WriteLine(type);
```

**注意**：反射一般需要引用`using System; `和 `using System.Reflection;`这两个命名空间。

使用反射获取已加载的程序集的完整名称：

**例如，**

```
//使用反射获取程序集的信息:
Assembly info = typeof(int).Assembly;
Console.WriteLine(info);
//mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089
```

**注意：**C# 关键字 `protected` 和 `internal` 在中间语言 (IL) 中没有任何意义，且不会用于反射 API 中。 在 IL 中对应的术语为“系列”和“程序集”。 若要标识 `internal `使用反射的方法，请使用 [IsAssembly](https://docs.microsoft.com/zh-cn/dotnet/api/system.reflection.methodbase.isassembly) 属性。 若要标识 protected internal 方法，请使用 [IsFamilyOrAssembly](https://docs.microsoft.com/zh-cn/dotnet/api/system.reflection.methodbase.isfamilyorassembly)。

**相关文档**：[.NET(C#) 中的程序集](https://www.cjavapy.com/article/1908/)

## 3、C# 反射使用实例

1）使用反映将一个对象的同名属性赋值给另一个对象

```c#
using System;
using System.Reflection;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
namespace ConsoleApplication
{
    public class VisitorInfo
    {
        public string openid { get; set; }
        public string wechatnick { get; set; }
        public string headurl { get; set; }
        public string mobile { get; set; }
        public string encryptedData { get; set; }
        public string iv { get; set; }
        public string session_key { get; set; }
    }
    public class MyObj
    {
        public string openid { get; set; }
        public string wechatnick { get; set; }
        public string headurl { get; set; }
        public string mobile { get; set; }
        public string encryptedData { get; set; }
        public string iv { get; set; }
        public string session_key { get; set; }
    }
    class Program
    {
       //为了适应更多情况，可以考虑用泛型改写
        public static VisitorInfo ToPageInfo(object model)
        {
            VisitorInfo v = new VisitorInfo();
            PropertyInfo property = null;
            foreach (var item in typeof(VisitorInfo).GetProperties())
            {
                property = model.GetType().GetProperty(item.Name);
                if (property != null)
                {
                    item.SetValue(v, property.GetValue(model));
                }
            }
            return v;
        }
        static void Main(string[] args)
        {
            MyObj myObj = new MyObj()
            {
                openid = "cjavapy",
                wechatnick = "liangliang",
                headurl = "https://www.cjavapy.com",
                mobile = "5201314",
                encryptedData = "",
                iv = "",
                session_key = ""
            };
            var v = ToPageInfo(myObj);
            Console.WriteLine(v.openid);
        }
    }
}
```

2）利用反映将DataTable转换成实现体对象

```c#
using System;using System.Reflection;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Data;
namespace ConsoleApplication
{
    public class VisitorInfo
    {
        public string openid { get; set; }
        public string wechatnick { get; set; }
        public string headurl { get; set; }
        public string mobile { get; set; }
        public string encryptedData { get; set; }
        public string iv { get; set; }
        public string session_key { get; set; }
    }
     // <summary>
    /// 将datatable装入指定类型的集合
    /// </summary>
    /// <typeparam name="T"></typeparam>
    public class MyList<T>:List<T>
    {
        public MyList(DataTable dt)
        {
            System.Type tt = typeof(T);//获取指定名称的类型
            object ff = Activator.CreateInstance(tt, null);//创建指定类型实例
            PropertyInfo[] fields = ff.GetType().GetProperties();//获取指定对象的所有公共属性
            foreach (DataRow dr in dt.Rows)
            {
                object obj = Activator.CreateInstance(tt, null);
                foreach (DataColumn dc in dt.Columns)
                {
                    foreach (PropertyInfo t in fields)
                    {
                        if (dc.ColumnName == t.Name)
                        {
                            t.SetValue(obj, dr[dc.ColumnName], null);//给对象赋值
                            continue;
                        }
                    }
                }
                this.Add((T)obj);//将对象填充到list集合
            }
        }
    }
    class Program
    {
        static void Main(string[] args)
        {
            /*MyObj myObj = new MyObj()
            {
                openid = "cjavapy",
                wechatnick = "liangliang",
                headurl = "https://www.cjavapy.com",
                mobile = "5201314",
                encryptedData = "",
                iv = "",
                session_key = ""
            };*/
            //从数据库中查询获取dt
            DataTable dt=null;
            var myList = new MyList<VisitorInfo>(dt);
        }
    }
}
```



## 4、使用反射获取标签(Attribute)

```c#
using System;
using System.Reflection;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
namespace ConsoleApplication
{
    /* 特性说明
      特性本质是一个继承和使用了系统基类的"类"，用以将元数据或声明信息与代码（程序集、类型、方法、属性等）相关联。特性与程序实体关联后，
      即可在运行时使用名为“反射”的技术查询特性。
      官方介绍的很详细，我们就一起来了解一下它的用法。
      特性具有以下属性：
      1.特性可向程序中添加元数据。元数据是有关在程序中定义的类型的信息。所有的.NET 程序集都包含指定的一组元数据，
        这些元数据描述在程序集中定义的类型和类型成员。可以添加自定义特性，以指定所需的任何附加信息。
      2.可以将一个或多个特性应用到整个程序集、模块或较小的程序元素（如类和属性）。
      3.特性可以与方法和属性相同的方式接受参数。
      4.程序可以使用反射检查自己的元数据或其他程序内的元数据。
      */
    #region 声明一个 作用于成员方法 的特性
    //1.第一步 继承自定义属性的基类 Attribute
    //2.第二步 指定 我们自定义特性类 的一些属性，用系系统特性指定
    // AttributeTargets：指定为程序中的何种成员设置属性 可写多个 例： AttributeTargets.Class|AttributeTargets.Method
    //    AllowMultiple：如果允许为一个实例多次指定该特性，则为 true；否则为 false。默认为 false。
    //        Inherited：如果该属性可由派生类和重写成员继承，则为 true，否则为 false。默认为 true。
    [AttributeUsage(AttributeTargets.Method, AllowMultiple = true, Inherited = true)]
    public class MethodAttribute : Attribute
    {
        /// <summary>
        /// 方法名称
        /// </summary>
        public string MethodName { set; get; }
        /// <summary>
        /// 方法描述
        /// </summary>
        public string MethodDesc { set; get; }
        /// <summary>
        /// 方法描述特性
        /// </summary>
        /// <param name="methodName">方法名称</param>
        /// <param name="methodDescription">方法描述</param>
        public MethodAttribute(string methodName, string methodDescription)
        {
            MethodName = methodName;
            MethodDesc = methodDescription;
        }
    }
    #endregion
    #region 声明一个 作用于类 的特性
    [AttributeUsage(AttributeTargets.Class, AllowMultiple = true)]//第一步
    public class ClassAttribute : Attribute//第二步
    {
        string ClassName { set; get; }
        string ClassDesc { set; get; }
        public ClassAttribute(string className, string classDesc)
        {
            ClassName = className;
            ClassDesc = classDesc;
        }
        //重写当前特性的ToString()方法
        public override string ToString()
        {
            return string.Format(@"当前类特性名:[ClassAttribute]
特性成员1:ClassName-->值：{0},
特性成员2：ClassDesc-->值：{1}", ClassName, ClassDesc);
        }
    }
    #endregion
    [ClassAttribute("TestClass", "测试反射及特性")]
    public class VisitorInfo
    {
        public string openid { get; set; }
        public string wechatnick { get; set; }
        [MethodAttribute("Demo1", "测试特性1")]
        public string GetHeadurl()
        {
            return "cjavapy";
        }
        public string mobile { get; set; }
        [MethodAttribute("Demo2", "测试特性2")]
        public string EncryptedData()
        {
            return "cjavapy";
        }
        public string iv { get; set; }
        public string session_key { get; set; }
    }

    class Program
    {
        static void Main(string[] args)
        {
            //得到当前方法上的特性类 信息
            MethodAttribute methodAttribute = (MethodAttribute)Attribute.GetCustomAttribute(typeof(VisitorInfo).GetMethod("GetHeadurl"), typeof(MethodAttribute));
            if (methodAttribute != null)
            {
                Console.WriteLine("输出当前方法特性信息:");
                Console.WriteLine("①当前调用方法名称: [" + methodAttribute.MethodName + "]");
                Console.WriteLine("②方法描述: [" + methodAttribute.MethodDesc + "]");
            }
            else
            {
                Console.WriteLine("当前传入的方法没有反射信息!");
            }
            Attribute attribute = Attribute.GetCustomAttribute(typeof(VisitorInfo), typeof(Attribute));
            if (attribute != null)
            {
                Console.WriteLine(attribute.ToString());
            }

        }
    }
}
```