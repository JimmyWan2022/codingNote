## C# Dapper SQL Builder

https://riptutorial.com/dapper-sqlbuilder/learn/100002/sql-builder

## 数据库设置

To use **Dapper.SqlBuilder**, we need to create a database first, and then we will perform various database operations using the **Dapper.SqlBuilder** library.

The SQL `CREATE DATABASE` statement is used to create a new SQL database.

```csharp
USE [master]
GO

CREATE DATABASE [BookStoreDb]

GO

USE [BookStoreDb]

GO

CREATE TABLE [dbo].[Authors](
	[Id] [int] IDENTITY(1,1) NOT NULL,
	[FirstName] [nvarchar](450) NULL,
	[LastName] [nvarchar](450) NULL,
 CONSTRAINT [PK_Authors] PRIMARY KEY CLUSTERED 
(
	[Id] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]
GO

CREATE TABLE [dbo].[Books](
	[Id] [int] IDENTITY(1,1) NOT NULL,
	[Title] [nvarchar](450) NULL,
	[Category] [nvarchar](max) NULL,
	[AuthorId] [int] NOT NULL,
 CONSTRAINT [PK_Books] PRIMARY KEY CLUSTERED 
(
	[Id] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
GO
```

It will create a `BookStoreDb` database that contains two tables. Now we need to insert some data into these tables, which can be used later.

The following SQL statements insert new records in the **Authors** and **Books** tables.

```csharp
USE [BookStoreDb]

GO

INSERT INTO Authors(FirstName, LastName) VALUES ('Cardinal','Tom B. Erichsen');
INSERT INTO Authors(FirstName, LastName) VALUES ('William','Shakespeare');
INSERT INTO Authors(FirstName, LastName) VALUES ('Robert','T. Kiyosaki');

INSERT INTO Books(Title, Category, AuthorId) VALUES ('Introduction to Machine Learning', 'Software', 1);
INSERT INTO Books(Title, Category, AuthorId) VALUES ('Introduction to Computing', 'Software', 1);
INSERT INTO Books(Title, Category, AuthorId) VALUES ('Romeo and Juliet', 'Humor & Entertainment', 2);
INSERT INTO Books(Title, Category, AuthorId) VALUES ('The Tempest', 'Fiction', 2);
INSERT INTO Books(Title, Category, AuthorId) VALUES ('The Winter''s Tale : Third Series', 'Fiction', 2);
INSERT INTO Books(Title, Category, AuthorId) VALUES ('Rich Dad, Poor Dad', 'Economics', 3);
```

Let's try the following queries to test the data in the database.

```csharp
Select * From Authors;

Select * From Books;
```

Let's click on the **Execute** button, and you will see the results of the above queries.

![image](https://raw.githubusercontent.com/zzzprojects/learn-orm/master/tutorials/dapper-sqlbuilder/images/database-setup.png)



## 使用



**Dapper.SqlBuilder** library provides various methods to build your SQL queries dynamically. The following example builds a simple `SELECT` query to retrieve all the authors from the database.

```csharp
private static List<Author> GetAuthors()
{
    using (IDbConnection connection = new SqlConnection(ConnectionString))
    {
        var builder = new SqlBuilder();
        builder.Select("Id");
        builder.Select("FirstName");
        builder.Select("LastName");

        var builderTemplate = builder.AddTemplate("Select /**select**/ from Authors");

        var authors = connection.Query<Author>(builderTemplate.RawSql).ToList();

        return authors;
    }
}
```

You can also build the SQL query containing the `WHERE` clause using the `DynamicParameters` as shown below.

```csharp
private static Author GetAuthor(int id)
{
    using (IDbConnection connection = new SqlConnection(ConnectionString))
    {
        var builder = new SqlBuilder();
        builder.Select("Id");
        builder.Select("FirstName");
        builder.Select("LastName");

        DynamicParameters parameters = new DynamicParameters();
        parameters.Add("@MyParam", id, DbType.Int32, ParameterDirection.Input);

        builder.Where("Id = @MyParam", parameters);

        var builderTemplate = builder.AddTemplate("Select /**select**/ from Authors /**where**/ ");

        var author = connection.Query<Author>(builderTemplate.RawSql, builderTemplate.Parameters).FirstOrDefault();

        return author;
    }
}
```

The following example builds the SQL query, which contains an inner join between `Authors` and `Books`.

```csharp
private static List<Author> GetAuthorWithBooks()
{
    var builder = new SqlBuilder();
    builder.Select("*");

    builder.InnerJoin("Books on Books.AuthorId=Authors.Id");

    var builderTemplate = builder.AddTemplate("Select /**select**/ from Authors /**innerjoin**/ ");

    var authorDictionary = new Dictionary<int, Author>();

    using (IDbConnection db = new SqlConnection(ConnectionString))
    {
        var authors = db.Query<Author, Book, Author>(
            builderTemplate.RawSql,
            (author, book) =>
            {
                Author authorEntry;

                if (!authorDictionary.TryGetValue(author.Id, out authorEntry))
                {
                    authorEntry = author;
                    authorEntry.Books = new List<Book>();
                    authorDictionary.Add(authorEntry.Id, authorEntry);
                }

                authorEntry.Books.Add(book);
                return authorEntry;
            },
            splitOn: "Id")
        .Distinct()
        .ToList();

        return authors;
    }
}
```