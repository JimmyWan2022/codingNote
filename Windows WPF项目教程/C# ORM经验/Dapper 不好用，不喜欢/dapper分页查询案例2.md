## Dapper 扩展功能

包括CRUD，分页查询等功能

https://github.com/tmsmith/Dapper-Extensions

![image-20230213201618107](dapper%E5%88%86%E9%A1%B5%E6%9F%A5%E8%AF%A2%E6%A1%88%E4%BE%8B2.assets/image-20230213201618107.png)

![image-20230213201636454](dapper%E5%88%86%E9%A1%B5%E6%9F%A5%E8%AF%A2%E6%A1%88%E4%BE%8B2.assets/image-20230213201636454.png)

dapper官网教程：读取数据库

```c#
//private static string connectionString = "Data Source=" + Environment.CurrentDirectory + "\\Recources\\sqlite3.db;";
        private static string connectionString = "Data Source=D:\\C# WPF\\WpfApp1\\WpfApp1\\bin\\Debug\\Recources\\sqlite3.db";
        public void readAsync() {
            Console.WriteLine("读取数据库:" + connectionString);
            using (var connection = new SQLiteConnection(connectionString))
            {
                // Create a query that retrieves all authors"    
                var sql = "SELECT * FROM Person;";
                // Use the Query method to execute the query and return the first author
                var author = connection.Query<Person>(sql);
                Console.WriteLine("读取数据库:" + author.ToString());
            }
        }
```

