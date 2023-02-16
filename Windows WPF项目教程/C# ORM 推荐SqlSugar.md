## 国内开发运营的c# ORM ：**[SqlSugar](https://github.com/DotNetNext/SqlSugar)**

| .net framework           | .net core                    |
| ------------------------ | ---------------------------- |
| Install-Package SqlSugar | Install-Package SqlSugarCore |



Furion框架 https://furion.baiqian.ltd/

SqlSugar案例

```c#
        public static void queryBySqlSugar() {

            SqlSugarClient Db = new SqlSugarClient(new ConnectionConfig()
            {
                //ConnectionString = "连接符字串",
                ConnectionString = "Data Source=D:\\C# WPF\\WpfApp1\\WpfApp1\\bin\\Debug\\Recources\\sqlite3.db",
                DbType = SqlSugar.DbType.Sqlite,
                IsAutoCloseConnection = true
            },
             db => {
                 //5.1.3.24统一了语法和SqlSugarScope一样，老版本AOP可以写外面

                 db.Aop.OnLogExecuting = (sql, pars) =>
                 {
                     Console.WriteLine(sql);//输出sql,查看执行sql 性能无影响
                     Console.WriteLine(pars);//输出sql,查看执行sql 性能无影响

                     //5.0.8.2 获取无参数化SQL 对性能有影响，特别大的SQL参数多的，调试使用
                     //UtilMethods.GetSqlString(DbType.SqlServer,sql,pars)
                 };
                   //注意多租户 有几个设置几个
                   //db.GetConnection(i).Aop

             });
            var list = Db.Queryable<Person>().ToList();
            Console.WriteLine("读取数据库:" + list.Count());

        }
```

