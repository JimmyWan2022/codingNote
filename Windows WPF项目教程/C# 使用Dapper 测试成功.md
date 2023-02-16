学习经验的网站：https://begincodingnow.com/wpf-sqlite-dapper-list-and-add-people-2/

dapper 教程 gitbook

https://esofar.gitbooks.io/dapper-tutorial-cn/content/

## 1.下载安装dapper

同时安装 System.Data.Sqlite.Core

![img](https://i0.wp.com/begincodingnow.com/wp-content/uploads/2020/05/SQLiteCore.png?resize=879%2C87&ssl=1)

## 2.放数据库进入编译成功的路径



##  3.配置参数

在App.config文件配置（官方提供的）

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Default" connectionString="Data Source=.\DemoDb.db;Version=3;" providerName="System.Data.SqlClient"/>
  </connectionStrings>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
</configuration>
```

需要新增到自己App.config文件中的部分

```
 <connectionStrings>
    <add name="Default" connectionString="Data Source=.\DemoDb.db;Version=3;" providerName="System.Data.SqlClient"/>
  </connectionStrings>
```





完整文件(不需要参考)

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <configSections>
    <section name="oracle.manageddataaccess.client" type="OracleInternal.Common.ODPMSectionHandler, Oracle.ManagedDataAccess, Version=4.122.21.1, Culture=neutral, PublicKeyToken=89b483f429c47342" />
  </configSections>
  <connectionStrings>
    <add name="Default" connectionString="Data Source=.\Recources\sqlite3.db;Version=3;" providerName="System.Data.SqlClient"/>
  </connectionStrings>
  <startup>
    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.8" />
  </startup>
</configuration>
```



## 4.使用代码



```
        public static void queryByDapper() {

            using (IDbConnection conn = new SQLiteConnection(LoadConnectionString())) { 
                var sql = "select * from Person";
                var aaa = conn.Query<Person>(sql);
                Console.WriteLine("读取数据库:" + aaa.Count());
            }
}
        private static string LoadConnectionString(string id = "Default")
        {
            return ConfigurationManager.ConnectionStrings[id].ConnectionString;
        }
```



Person数据库

sql 创建表语句

```sql
CREATE TABLE Person
(

    [Id] INT PRIMARY KEY,

    [FirstName] NVARCHAR(256) NULL,
	[LastName] NVARCHAR(256) NULL,
	[Active] INT NULL,

    [DateCreated] DATETIME NULL
)
```



插入数据

```
INSERT INTO Person (FirstName,LastName,Active)
VALUES ('Paul', 'bill', '1');
```



Bean

Bean to Sql http://www.lzltool.com/CsharpDto2SqlServer

```
    public class Person
    {
        public int Id { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public bool Active { get; set; }
        public DateTime DateCreated { get; set; }
    }
```

