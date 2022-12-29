## C# 异步编程(async和await)

在C#中，如果需要 I/O 绑定（例如从网络请求数据、访问数据库或读取和写入到文件系统），则需要利用异步编程。 还可以使用 CPU 绑定代码（例如执行成本高昂的计算），对编写异步代码而言，写法简单易用。异步编程其实也就是Task实现的多线程。本文主要介绍C# 异步编程(async和await)。

## 1、异步编程简介

异步编程的核心是 `Task` 和 `Task<T> `对象，这两个对象对异步操作建模。 它们受关键字 `async` 和 `await` 的支持。 在大多数情况下模型十分简单：

对于 I/O 绑定代码，等待一个在 `async` 方法中返回 `Task` 或 `Task<T>` 的操作。

对于 CPU 绑定代码，等待一个使用 `Task.Run` 方法在后台线程启动的操作。

通过使用异步编程，可以避免性能瓶颈并增强应用程序的总体响应能力。 但是，编写异步应用程序的传统技术可能比较复杂，使它们难以编写、调试和维护。C# 5 引入了一种简便方法，即异步编程。此方法利用了 .NET Framework 4.5 及更高版本、.NET Core 和 Windows 运行时中的异步支持。 编译器可执行开发人员曾进行的高难度工作，且应用程序保留了一个类似于同步代码的逻辑结构。 因此，只需做一小部分工作就可以获得异步编程的所有好处。



`await` 关键字有这奇妙的作用。 它控制执行 `await` 的方法的调用方，且它最终允许 UI 具有响应性或服务具有灵活性。 使用`async`和`await`实现的异步代码，也可以使用`Task`对象的`Wait()`等方法替代。

`Thread`和`Task`使用可以参考：[C# 多线程(Thread和Task)](https://www.cjavapy.com/article/1917/)

1）通过指定Url下载数据(I/O 绑定)

希望点击按钮时从指定Url下载某些数据，但不希望阻止 UI 线程。 此时可以异步编程。

**例如，**

```c#
private readonly HttpClient _httpClient = new HttpClient();
downloadButton.Clicked += async(o, e) =>
{
    //这一行将把控制交给UI作为请求
    //从web服务正在下载。
    // UI线程现在可以自由执行其他工作。
    var stringData = await _httpClient.GetStringAsync(URL);
    DoSomethingWithData(stringData);
};
```

2）执行游戏计算(CPU绑定)

编写游戏时，按下某个按钮，将会对许多敌人造成伤害。执行伤害计算的开销可能极大，而且在 UI 线程中执行计算有可能使游戏在计算执行过程中卡住。

解决方法是启动一个后台线程，它使用 `Task.Run` 执行工作，并使用 `await` 等待其结果。 这可确保在执行工作时 UI 能流畅运行。

**例如，**

```c#
private DamageResult CalculateDamageDone()
{
    //省略实现代码
    //执行一个耗时的计算并返回
    //计算的结果。
}
calculateButton.Clicked += async(o, e) =>
{
    //该行将在CalculateDamageDone（）时将控制权交给用户界面
    //执行它的工作。UI线程可以自由地执行其他工作。
    var damageResult = await Task.Run(() => CalculateDamageDone());
DisplayDamage(damageResult);
};
```



异常编程实现可以无需手动管理后台线程，而是通过非阻止性的方式来实现。

## 2、从网站Url获取返回的内容

使用 ASP.NET 定义 Web API 控制器方法，该方法将执行此任务并返回的内容。

**例如，**

```c#
private readonly HttpClient _httpClient = new HttpClient();
[HttpGet, Route("GetContent")]
public async Task<int> GetContent()
{
    //挂起GetContent()来允许调用者(web服务器)
    //接收另一个请求，而不是阻塞这个请求。
    var result = await _httpClient.GetStringAsync("https://dotnetfoundation.org");
    return result;
}
```



使用WinForm通过点击按钮使用`HttpClient`异步请求获取内容：

**例如，**

```c#
private readonly HttpClient _httpClient = new HttpClient();
private async void OnGetContentButtonClick(object sender, RoutedEventArgs e)
{
    // //捕获任务句柄，以便稍后等待后台任务。
    var getContentFoundationHtmlTask = _httpClient.GetStringAsync("https://dotnetfoundation.org");
    // UI线程上的任何其他工作都可以在这里完成，例如启用一个进度条。
    //这是很重要的，在"await"调用之前，以便用户
    //在执行此方法之前查看进度条。
    NetworkProgressBar.IsEnabled = true;
    NetworkProgressBar.Visibility = Visibility.Visible;
    //等待操作符挂起OnGetContentButtonClick()，将控制返回给调用者。
    //这是什么允许应用程序响应，而不是阻塞UI线程。
    var result = await GetContentFoundationHtmlTask;
    DotNetCountLabel.Text = result;
    NetworkProgressBar.IsEnabled = false;
    NetworkProgressBar.Visibility = Visibility.Collapsed;
}
```



## 3、等待多个任务完成

可能需要并行检索多个数据部分的情况。 Task API 包含两种方法（即 `Task.WhenAll` 和 `Task.WhenAny`），这些方法允许你编写在多个后台作业中执行非阻止等待的异步代码。

**例如，**

```c#
public async Task<User> GetUserAsync(int userId)
{
    // 省略实现代码:
    //给定用户Id {userId}，检索对应的user对象
    //在数据库中使用{userId}作为Id的条目。
}
public static async Task<IEnumerable<User>> GetUsersAsync(IEnumerable<int> userIds)
{
    var getUserTasks = new List<Task<User>>();
    foreach (int userId in userIds)
    {
        getUserTasks.Add(GetUserAsync(userId));
    }
    return await Task.WhenAll(getUserTasks);
}
```



或者，使用 LINQ实现：

**例如，**

```c#
public async Task<User> GetUserAsync(int userId)
{
    // 省略实现代码:
    //给定用户Id {userId}，检索对应的user对象
    //在数据库中使用{userId}作为Id的条目。
}
public static async Task<User[]> GetUsersAsync(IEnumerable<int> userIds)
{
    var getUserTasks = userIds.Select(id => GetUserAsync(id));
    return await Task.WhenAll(getUserTasks);
}
```



**注意**：将LINQ 和异步代码一起使用时需要谨慎。 因为 LINQ 使用延迟的执行，因此异步调用将不会像在 `foreach` 循环中那样立刻发生，除非通过调用 `.ToList()` 或 `.ToArray() `强制生成序列。

## 4、异步方法易于编写

C# 中的 `async` 和 `await` 关键字是异步编程的核心。 通过这两个关键字，可以使用 .NET Framework、.NET Core 或 Windows 运行时中的资源，轻松创建异步方法（几乎与创建同步方法一样轻松）。 使用 `async` 关键字定义的异步方法简称为“异步方法”。

**例如，**

```c#
public async Task<int> GetUrlContentLengthAsync()
{
    var client = new HttpClient();

    Task<string> getStringTask =
        client.GetStringAsync("https://docs.microsoft.com/dotnet");

    DoIndependentWork();

    string contents = await getStringTask;

    return contents.Length;
}

void DoIndependentWork()
{
    Console.WriteLine("Working...");
}
```

**注意**：从方法签名开始。 它包含 `async` 修饰符。 返回类型为 `Task<int>`。方法名称以 `Async` 结尾。 在方法的主体中，`GetStringAsync` 返回 `Task<string>`。 `await` 运算符。 它会暂停 `GetUrlContentLengthAsync`，当 `getStringTask` 完成时，控件将在此处继续。

如果 `GetUrlContentLengthAsync` 在调用 `GetStringAsync` 和等待其完成期间不能进行任何工作，则你可以通过在下面的单个语句中调用和等待来简化代码。

**例如，**

```
string contents = await client.GetStringAsync("https://docs.microsoft.com/dotnet");
```

1）方法签名包含 `async` 修饰符。

2）按照约定，异步方法的名称以`“Async”`后缀结尾。

3）返回类型为下列类型之一：



- 如果方法有操作数为 `TResult` 类型的返回语句，则为 `Task<TResult>`。
- 如果方法没有返回语句或具有没有操作数的返回语句，则为 `Task`。
- void：如果要编写异步事件处理程序。
- 包含 `GetAwaiter` 方法的其他任何类型（自 C# 7.0 起）。



4）方法通常包含至少一个 `await` 表达式，该表达式标记一个点，在该点上，直到等待的异步操作完成方法才能继续。 同时，将方法挂起，并且控件返回到方法的调用方。

## 5、API异步方法以及async和await关键字

.NET Framework 4.5 或更高版本以及 .NET Core 包含许多可与 `async` 和 `await` 结合使用的成员。 可以通过追加到成员名称的“`Async”`后缀和 `Task` 或 `Task<TResult>` 的返回类型，识别这些成员。 例如，`System.IO.Stream` 类包含 `CopyToAsync`、`ReadAsync` 和 `WriteAsync` 等方法，以及同步方法 `CopyTo`、`Read` 和 `Write`。

异步方法旨在成为非阻止操作。 异步方法中的 `await` 表达式在等待的任务正在运行时不会阻止当前线程。 相反，表达式在继续时注册方法的其余部分并将控件返回到异步方法的调用方。`async` 和 `await` 关键字不会创建其他线程。 因为异步方法不会在其自身线程上运行，因此它不需要多线程。

只有当方法处于活动状态时，该方法将在当前同步上下文中运行并使用线程上的时间。 可以使用 Task.Run 将占用大量 CPU 的工作移到后台线程，但是后台线程不会帮助正在等待结果的进程变为可用状态。

**async 和 await的作用：**

1）标记的异步方法可以使用 `await` 来指定暂停点。 `await` 运算符通知编译器异步方法：在等待的异步过程完成后才能继续通过该点。 同时，控制返回至异步方法的调用方。 异步方法在 await 表达式执行时暂停并不构成方法退出，只会导致 `finally` 代码块不运行。

2）标记的异步方法本身可以通过调用它的方法等待。

异步方法通常包含 `await` 运算符的一个或多个实例，但缺少 `await` 表达式也不会导致生成编译器错误。 如果异步方法未使用 `await` 运算符标记暂停点，则该方法会作为同步方法执行，即使有 `async` 修饰符，也不例外。 编译器将为此类方法发布一个警告。

## 6、使用 IAsyncEnumerable<T> 的异步流

从 C# 8.0 开始，异步方法可能返回异步流，由 [IAsyncEnumerable](https://docs.microsoft.com/zh-cn/dotnet/api/system.collections.generic.iasyncenumerable-1) 表示 。 异步流提供了一种方法，来枚举在具有重复异步调用的块**中生成元素时从流中读取的项。**

**例如，**

```c#
static async IAsyncEnumerable<string> ReadWordsFromStreamAsync()
{
    string data =
        @"https://www.cjavapy.com";

    using var readStream = new StringReader(data);

    string line = await readStream.ReadLineAsync();
    while (line != null)
    {
        foreach (string word in line.Split(' ', StringSplitOptions.RemoveEmptyEntries))
        {
            yield return word;
        }

        line = await readStream.ReadLineAsync();
    }
}
```