## dapper分页查询



https://www.learmoreseekmore.com/2021/08/demo-on-dotnet5-web-api-pagination-using-dapper-orm.html

### Create A Sample Table:

To accomplish our demo let's create a sample table.

```
CREATE TABLE [dbo].[Todo](
	[Id] [int] IDENTITY(1,1) NOT NULL,
	[ItemName] [varchar](max) NULL,
	[IsCompleted] [bit] NOT NULL
	CONSTRAINT Pk_Todo PRIMARY KEY (Id)
)
```

### Create A .Net5 Web API Application:

Let's create a .Net5 Web API application to implement our pagination.

![img](https://1.bp.blogspot.com/-MpwOIn4WwAg/YQk9zUsWxSI/AAAAAAAAr4w/J_UY8pGPbs4_JImq22RMVZHn7AIHoqw9QCPcBGAYYCw/s16000/2.JPG)

Specify the project name and solution name

[![img](https://1.bp.blogspot.com/-OhQJTRg_MjQ/YQlCDighJlI/AAAAAAAAr40/JcGmjU1BNLsudwOLh7aFrDto5NG7UaMuwCLcBGAsYHQ/s16000/3.JPG)](https://1.bp.blogspot.com/-OhQJTRg_MjQ/YQlCDighJlI/AAAAAAAAr40/JcGmjU1BNLsudwOLh7aFrDto5NG7UaMuwCLcBGAsYHQ/s994/3.JPG)

Select target framework a .Net5

[![img](https://1.bp.blogspot.com/-fDFZv3DjXFA/YQlGlockjWI/AAAAAAAAr48/8xui61KZDrsqmCAKyzldbM20_6o2_Q2_wCLcBGAsYHQ/s16000/4.JPG)](https://1.bp.blogspot.com/-fDFZv3DjXFA/YQlGlockjWI/AAAAAAAAr48/8xui61KZDrsqmCAKyzldbM20_6o2_Q2_wCLcBGAsYHQ/s989/4.JPG)

### Create Table Model and Pagination Response Model:

Now we need to create to models like 'Todo'(Table Class) and 'PagingResponseModel'(Class that used as API response model which contains all pagination properties). So let's add a new folder like 'Models' and our 2 classes inside of it.

***Models/Todo.cs:\***

```
namespace Dot5.API.Dapper.Pagging.Models
{
    public class Todo
    {
        public int Id { get; set; }
        public string ItemName { get; set; }
        public bool IsCompleted { get; set; }
    }
}
```

***Models/PagingResponseModel.cs:\***

```
using System;
 
namespace Dot5.API.Dapper.Pagging.Models
{
    public class PagingResponseModel<T> where T : class
    {
        public int TotalRecords { get; set; }
        public int CurrentPageNumber { get; set; }
        public int PageSize { get; set; }
        public int TotalPages { get; set; }
        public bool HasNextPage { get; set; }
        public bool HasPreviousPage { get; set; }
        public T Data { get; set; }
        public PagingResponseModel(T data, int totalRecords, int currentPageNumber, int pageSize)
        {
            Data = data;
            TotalRecords = totalRecords;
            CurrentPageNumber = currentPageNumber;
            PageSize = pageSize;
 
            // total pages count
            TotalPages = Convert.ToInt32(Math.Ceiling(((double)TotalRecords / (double)pageSize)));
 
            HasNextPage = CurrentPageNumber < TotalPages;
            HasPreviousPage = CurrentPageNumber > 1;
        }
    }
}
```

- (Line: 5) The 'PagingResponseModel<T>' is a generic response model that can be used by any endpoint that wants to use the features of the pagination.
- (Line: 22) Calculating the 'TotalPages' that need to be shown on the UI application. So to finalize the 'TotalPages' value we have to divide the 'TotalRecords' by the 'PageSize' and then the decimal result will be rounded to max value using 'Math.Ceiling()'.

### SQL Query Use OFFSET-FETCH For Pagination:

In this demo, we will use DAPPER ORM which deals with raw queries. So let's understand the pagination SQL raw query.

```
SELECT  * FROM Todo
ORDER BY Id
OFFSET @Skip ROWS FETCH NEXT @Take ROWS ONLY
```

### Install NuGet:

In our demo, we will use Dapper ORM for database communication. So install the Dapper NuGet.

![img](https://1.bp.blogspot.com/-UCQNWKsjTE0/YQli1sSLMnI/AAAAAAAAr5E/f4NnCE6hebsXwh1-auR8JJ4H_ce6CcpOACLcBGAsYHQ/s16000/5.JPG)

Now install the 'SqlClient' library to read connection. 

[![img](https://1.bp.blogspot.com/-LV6di-cdPQk/YQlmyjj3JJI/AAAAAAAAr5M/jvDqgKZEiCc5LThqnC8OBeWnfB4ejCjxQCLcBGAsYHQ/s16000/6.JPG)](https://1.bp.blogspot.com/-LV6di-cdPQk/YQlmyjj3JJI/AAAAAAAAr5M/jvDqgKZEiCc5LThqnC8OBeWnfB4ejCjxQCLcBGAsYHQ/s1307/6.JPG)

### Create Todo API Endpoint:

Let's create a new controller like 'TodosController.cs'.

***Controllers/TodosController.cs:\***

```
using Dapper;
using Dot5.API.Dapper.Pagging.Models;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;
using System.Data.SqlClient;
using System.Linq;
using System.Threading.Tasks;
 
namespace Dot5.API.Dapper.Pagging.Controllers
{
    [Route("api/[controller]")]
    [ApiController]
    public class TodosController : ControllerBase
    {
        private readonly IConfiguration _configuration;
        public TodosController(IConfiguration configuration)
        {
            _configuration = configuration;
        }
        [HttpGet]
        public IActionResult Get(int currentPageNumber, int pageSize)
        {
            int maxPagSize = 50;
            pageSize = (pageSize > 0 && pageSize <= maxPagSize) ? pageSize : maxPagSize;
 
            int skip = (currentPageNumber - 1) * pageSize;
            int take = pageSize;
 
            string query = @"SELECT 
                            COUNT(*)
                            FROM Todo
 
                            SELECT  * FROM Todo
                            ORDER BY Id
                            OFFSET @Skip ROWS FETCH NEXT @Take ROWS ONLY";
 
            using (var connection = new SqlConnection(_configuration.GetConnectionString("MyWorldDbConnection")))
            {
                var reader = connection.QueryMultiple(query, new { Skip = skip, Take = take });
 
                int count = reader.Read<int>().FirstOrDefault();
                List<Todo> allTodos = reader.Read<Todo>().ToList();
 
                var result = new PagingResponseModel<List<Todo>>(allTodos, count, currentPageNumber, pageSize);
                return Ok(result);
            }
        }
    }
}
```

- (Line: 19) Injected 'Microsoft.Extensions.Configuration.IConfiguration'.
- (Line: 26&27) Using 'maxPageSize' variable we are restricting the 'pageSize' query parameter.
- (Line: 29&30) Preparing the 'skip' and 'take' variables data.
- (Line: 32-28) The SQL raw query is defined here. Here we prepared 2 queries one to find the total count and the other for fetching records based on pagination conditions.
- (Line: 40) Initializing the 'SqlConnection' with database connection string as an input parameter.
- (Line: 42) The 'QueryMultiple' is a dapper method for returning multiple results sets.
- (Line: 44) Reading first result set that returns total records count.
- (Line: 45) Reading the second result set that returns 'Todo' table data filtered by pagination.
- (Line: 47) Preparing the 'PagingResponseModel<T>'.

Add the database connection string into the 'appsettings.Development.json' file.

***appSettings.Development.json:\***

```js
"ConnectionStrings": {
    "MyWorldDbConnection": "Your_ConnectionString"
}
```

Now run the application and test the output on the swagger page.

[![img](https://1.bp.blogspot.com/-_hrbFHOypMA/YQniWxLyqYI/AAAAAAAAr5Y/m-XhhzjUyqEGrh9WIOmPXYmhc0shAPiGwCLcBGAsYHQ/s16000/7.JPG)](https://1.bp.blogspot.com/-_hrbFHOypMA/YQniWxLyqYI/AAAAAAAAr5Y/m-XhhzjUyqEGrh9WIOmPXYmhc0shAPiGwCLcBGAsYHQ/s1299/7.JPG)

So that's all about a demo on implementing pagination in .Net5 Web API using Dapper ORM.