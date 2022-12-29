## .NET Core(C#) 字典Dictionary通过key值排序方法及示例代码

本文主要介绍.NET Core(C#)中，根据字典Dictionary的key值进行排序的方法，以及相关的示例代码。

## 1、先将key排序在添加排序后的key和value

```c#
using System;
using System.Collections.Generic;
namespace ConsoleApplication1 {
    class Program {
        static void Main(string[] args) {
            Dictionary<string,Point> r=new Dictionary<string,Point>();
            r.Add("c3",new Point(0,1));
            r.Add("c1",new Point(1,2));
            r.Add("t3",new Point(2,3));
            r.Add("c4",new Point(3,4));
            r.Add("c2",new Point(4,5));
            r.Add("t1",new Point(5,6));
            r.Add("t2",new Point(6,7));
            List<string> zlk=new List<string>(r.Keys);
            zlk.Sort();
            List<Point> zlv=new List<Point>();
            // Readd with the order.
            foreach(var item in zlk) {
                zlv.Add(r[item]);
            }
            r.Clear();
            for(int i=0;i<zlk.Count;i++) {
                r[zlk[i]]=zlv[i];
            }
            foreach(var item in r.Keys) {
                Console.WriteLine(item+" "+r[item].X+" "+r[item].Y);
            }
            Console.ReadKey(true);
        }
    }
}
```









## 2、通过Linq的OrderBy()排序

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
            Dictionary<int, string> test = new Dictionary<int, string> { };  
            test.Add(0,"C#");  
            test.Add(3, "Java");  
            test.Add(1, "Python");  
            test.Add(9, "cjavapy");  
            Dictionary<int, string> dic1Asc = test.OrderBy(o => o.Key).ToDictionary(o => o.Key, p => p.Value);  
            Console.WriteLine("小到大排序");  
            foreach(KeyValuePair<int,string> k in dic1Asc){  
                Console.WriteLine("key:" +k.Key +" value:" + k.Value);  
            }  
            Console.WriteLine("大到小排序");  
            Dictionary<int, string> dic1desc = test.OrderByDescending(o => o.Key).ToDictionary(o => o.Key, p => p.Value);  
            foreach (KeyValuePair<int, string> k in dic1desc)  
            {  
                Console.WriteLine("key:" + k.Key + " value:" + k.Value);  
            }  
        }  
    }  
}  
```