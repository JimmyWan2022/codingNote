## .NET(C#) Linq中join、into、let和group by的使用


Linq是Language Integrated Query的简称，它是微软在.NET Framework 3.5里面新加入的特性，用以简化查询查询操作。主要包含：Linq to Object、Linq to SQL、Linq to XML，其中Linq to Object和对于对象的查询，Linq to XML则又提供了对XML格式数据的检索、设置等方法，本文主要介绍.NET(C#) Linq中join、into、let和group by的使用。


## 1、into子句

`into`子句作为一个临时标识符，用于`group select join` 子句中。存储了`into`子句前面的查询内容，可以方便后面的子句的使用，对其进行再次查询或排序 投影等操作。

**例如，**

```
List<UserBaseInfo> users = new List<UserBaseInfo>();
for(int i=1;i<10;i++)
{
    users.Add(new UserBaseInfo(i,"users0"+i.ToString(),"user0"+i.ToString()+"@web.com"));
}
var result = from u in users
             group u by Int32.Parse(u.UserName.Substring(u.UserName.Length - 2)) % 2 == 0 into g
             where g.Count() < 5
             select g;
foreach (var v in result)
{
    foreach (UserBaseInfo u in v)
    {
        Response.Write(u.UserName + "</br>");
    }
}
```













## 2、let子句

let子句在LINQ表达式中存储子表达式的计算结果。let子句创建一个范围变量来存储结果，变量被创建后，不能修改或把其他表达式的结果重新赋值给它。此范围变量可以在后续的LINQ语句中使用。

**例如，**

```
string[] strings = { "hello world", "c java python", "js docker linux" };
var strs = from s in strings
           let words = s.Split(' ')
           from word in words
           let w = word.ToUpper()
           select w;
foreach(var s in strs)
{
    Console.WriteLine(s);
}
```

## 3、join子句

如一个数据源中元素的某一个属性可以跟另外一个数据源中元素的属性进行相等比较，则两个数据源可以用join子句进行关联。

1）内连接

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
namespace ConsoleApplication
{
    class Province
    {
        public int ProID { get; set; }
        public string ProName { get; set; }
    }
    class City
    {
        public string CityName { get; set; }
        public int ProID { get; set; }
    }
    class Program
    {
        static void Main(string[] args)
        {
            List<Province> Provinces = new List<Province>()
            {
                new Province(){ProID = 1,ProName="广东"},
                new Province(){ProID = 2,ProName="浙江"},
                new Province(){ProID = 3,ProName="江西"},
                new Province(){ProID = 4,ProName="湖北"},
            };
            List<City> Citys = new List<City>()
            {
                new City{CityName="深圳",  ProID=1},
                new City{CityName="广州",  ProID=1},
                new City{CityName="惠州",  ProID=1},
                new City{CityName="杭州",  ProID=2},
                new City{CityName="金华",  ProID=2},
                new City{CityName="景德镇",  ProID=3},
                new City{CityName="南昌",  ProID=3},
            };
            var query = from province in Provinces
                        join city in Citys on province.ProID equals city.ProID
                        select new { ProName = province.ProName, CityName = city.CityName };
            foreach (var q in query)
            {
                Console.WriteLine("{0,-5}{1}", q.ProName + ":", q.CityName);
            }
        }
    }
}
```







2）左外连接

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
namespace ConsoleApplication
{
    class Province
    {
        public int ProID { get; set; }
        public string ProName { get; set; }
    }
    class City
    {
        public string CityName { get; set; }
        public int ProID { get; set; }
    }
    class Program
    {
        static void Main(string[] args)
        {
            List<Province> Provinces = new List<Province>()
            {
                new Province(){ProID = 1,ProName="广东"},
                new Province(){ProID = 2,ProName="浙江"},
                new Province(){ProID = 3,ProName="江西"},
                new Province(){ProID = 4,ProName="湖北"},
            };
            List<City> Citys = new List<City>()
            {
                new City{CityName="深圳",  ProID=1},
                new City{CityName="广州",  ProID=1},
                new City{CityName="惠州",  ProID=1},
                new City{CityName="杭州",  ProID=2},
                new City{CityName="金华",  ProID=2},
                new City{CityName="景德镇",  ProID=3},
                new City{CityName="南昌",  ProID=3},
            };
            var query = from province in Provinces
                        join city in Citys on province.ProID equals city.ProID into result1
                        from province_city in result1.DefaultIfEmpty()
                        select new { province.ProName, CityName = (province_city == null ? String.Empty : province_city.CityName) };
            foreach (var v in query)
            {
                Console.WriteLine("{0,-5}{1}", v.ProName + ":", v.CityName);
            }
        }
    }
}
```





3）组连接

分组联接可用于产生分层数据结构。 它将第一个集合中的每个元素与第二个集合中的一组相关元素进行配对。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
namespace ConsoleApplication
{
    class Province
    {
        public int ProID { get; set; }
        public string ProName { get; set; }
    }
    class City
    {
        public string CityName { get; set; }
        public int ProID { get; set; }
    }
    class Program
    {
        static void Main(string[] args)
        {
            List<Province> Provinces = new List<Province>()
            {
                new Province(){ProID = 1,ProName="广东"},
                new Province(){ProID = 2,ProName="浙江"},
                new Province(){ProID = 3,ProName="江西"},
                new Province(){ProID = 4,ProName="湖北"},
            };
            List<City> Citys = new List<City>()
            {
                new City{CityName="深圳",  ProID=1},
                new City{CityName="广州",  ProID=1},
                new City{CityName="惠州",  ProID=1},
                new City{CityName="杭州",  ProID=2},
                new City{CityName="金华",  ProID=2},
                new City{CityName="景德镇",  ProID=3},
                new City{CityName="南昌",  ProID=3},
            };
            var query = from province in Provinces
                        join city in Citys on province.ProID equals city.ProID into gj
                        select new
                        {
                            ProName = province.ProName,
                            City = gj
                        };
            foreach (var v in query)
            {
                //输出省份
                Console.WriteLine("{0}:", v.ProName);
                //输出省份对应的城市
                foreach (City c in v.City)
                    Console.WriteLine("  {0}", c.CityName);
            }
        }
    }
}
```





## 4、group by

group by可以根据给定数据列的每个成员对查询结果进行分组统计，最终得到一个分组汇总表。

1）简单分组

```
var q =  
   from p in db.Products  
   group p by p.CategoryID into g  
   where g.Count() >= 10  
   select new {  
     g.Key,  
     ProductCount = g.Count()  
   }; 
```



2）最大值

```
var q =  
  from p in db.Products  
  group p by p.CategoryID into g  
  select new {  
    g.Key,  
    MaxPrice = g.Max(p => p.UnitPrice)  
  }; 
```

3）最小值

```
var q =  
   from p in db.Products  
   group p by p.CategoryID into g  
   select new {  
     g.Key,  
     MinPrice = g.Min(p => p.UnitPrice)  
   }; 
```

4）平均值

```
var q =  
   from p in db.Products  
   group p by p.CategoryID into g  
   select new {  
      g.Key,  
      AveragePrice = g.Average(p => p.UnitPrice)  
   }; 
```

5）求和

```
var q =  
   from p in db.Products  
   group p by p.CategoryID into g  
   select new {  
     g.Key,  
     TotalPrice = g.Sum(p => p.UnitPrice)  
   }; 
```

6）多列分组

```
var categories =  
   from p in db.Products  
   group p by new  
   {  
     p.CategoryID,  
     p.SupplierID  
   }  
   into g  
   select new  
   {  
     g.Key,  
     g  
   }; 
```

7）表达式

```
var categories =  
   from p in db.Products  
   group p by new { Criterion = p.UnitPrice > 10 } into g  
   select g; 
```