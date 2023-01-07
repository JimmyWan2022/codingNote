## C# 多线程(Thread和Task)

线程（Thread）是进程中的基本执行单元，是操作系统分配CPU时间的基本单位，一个进程可以包含若干个线程，在进程入口执行的第一个线程被视为这个进程的主线程。本文主要介绍C# Thread和Task实现多线程。

## 1、C# 线程介绍

进程作为操作系统执行程序的基本单位，拥有应用程序的资源，进程包含线程，进程的资源被线程共享，线程不拥有资源。通过Thread类新建线程默认为前台线程。当所有前台线程关闭时，所有的后台线程也会被直接终止，不会抛出异常。由于线程的执行顺序和程序的执行情况不可预知，所以使用挂起和唤醒容易发生死锁的情况，在实际应用中应该尽量少用。

使用线程的作用：

1）可以使用线程将代码同其他代码隔离，提高应用程序的可靠性。

2）可以使用线程来简化编码。

3）可以使用线程来实现并发执行。

## 2、线程的使用

C# 中多线程的使用可以通过`System.Threading.Thread `实现，也可以通过`System.Threading.Tasks.Task`实现.

`System.Threading.Thread` 类用于线程的工作。它允许创建并访问多线程应用程序中的单个线程。进程中第一个被执行的线程称为主线程。

当 C# 程序开始执行时，主线程自动创建。使用 `Thread` 类创建的线程被主线程的子线程调用。可以使用 `Thread` 类的 `CurrentThread` 属性访问线程。`Task`是.NET4.0加入的，与线程池`ThreadPool`的功能类似，用`Task`开启新任务时，会从线程池中调用线程，而`Thread`每次实例化都会创建一个新的线程。我们可以说`Task`是一种基于任务的编程模型。它与`Thread`的主要区别是，更加方便对线程进程调度和获取线程的执行结果。并且`Task`是针对多核有优化。

1）使用Thread

```
using System;using System.Threading;//简单的线程场景:启动一个静态方法运行
//第二个线程。
public class ThreadExample
{
 //线程启动时调用ThreadProc方法。
 //它循环10次，写入控制台和屈服
 //每个时间片的剩余部分，然后结束。
 public static void ThreadProc()
 {
 for (int i = 0; i < 10; i++)
 {
 Console.WriteLine("ThreadProc: {0}", i);
 //生成剩余的时间片。
 Thread.Sleep(0);
 }
 }
 public static void Main()
 {
 Console.WriteLine("主线程:启动第二个线程。");
 //线程类的构造函数需要一个ThreadStart
 //对象上要执行的方法的线程。
 //c#简化了这个委托的创建。
 Thread t = new Thread(new ThreadStart(ThreadProc));
 // 开始ThreadProc。请注意，在单处理器上，新的
 //线程没有得到任何处理器时间，直到主线程
 //被抢占或产生。取消线程。睡眠
 //使用t.Start()来查看区别。
 //可以通过提供委托来启动线程，该委托表示线程在其类构造函数中执行的方法。 然后调用 Start 方法开始执行。
 t.Start();
 //Thread.Sleep(0);
 for (int i = 0; i < 4; i++)
 {
 Console.WriteLine("主线程: 执行方法。");
 Thread.Sleep(0);
 }
 Console.WriteLine("主线程:调用Join()，等待线程进程结束。");
 t.Join();
 Console.WriteLine("主线程:ThreadProc。加入已恢复。按“Enter”结束程序。");
 Console.ReadLine();
 }
}
```

**注意**：进程启动时，公共语言运行时将自动创建单个前台线程以执行应用程序代码。 除了此主前台线程，进程还可以创建一个或多个线程来执行与进程关联的程序代码的一部分。 这些线程可以在前台或后台执行。 此外，还可以使用 [ThreadPool](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.threadpool?view=net-5.0) 类来执行由公共语言运行时管理的工作线程上的代码。

2）使用Task

下面的示例创建并执行四个任务。 三个任务执行 `Action<T>` 名为的委托 action ，该委托接受类型的参数 `Object` 。 第四个任务执行 lambda 表达式 (`Action` 委托) ，该委托在对任务创建方法的调用中以内联方式定义。 每个任务实例化并以不同的方式运行：

**例如，**

```
using System;
using System.Threading;
using System.Threading.Tasks;
class Example
{
 static void Main()
 {
 Action<object> action = (object obj) =>
 {
 Console.WriteLine("Task={0}, obj={1}, Thread={2}",
 Task.CurrentId, obj,
 Thread.CurrentThread.ManagedThreadId);
 };
 // 创建一个任务但不启动它。
 Task t1 = new Task(action, "alpha");
 // 构造一个已启动的任务
 Task t2 = Task.Factory.StartNew(action, "beta");
 // 阻塞主线程以演示t2正在执行
 t2.Wait();
 // 启动 t1 
 t1.Start();
 Console.WriteLine("t1 has been launched. (Main Thread={0})",
 Thread.CurrentThread.ManagedThreadId);
 // 等待任务完成。
 t1.Wait();
 // 使用Task.Run构造一个已启动的任务。
 String taskData = "delta";
 Task t3 = Task.Run(() => {
 Console.WriteLine("Task={0}, obj={1}, Thread={2}",
 Task.CurrentId, taskData,
 Thread.CurrentThread.ManagedThreadId);
 });
 // 等待任务完成。
 t3.Wait();
 // 构造一个未启动的任务
 Task t4 = new Task(action, "gamma");
 // 同步运行它
 t4.RunSynchronously();
 //虽然任务是同步运行的，但这是一个很好的实践
 //在任务抛出异常时等待它。
 t4.Wait();
 }
}
```

`Task` 可以通过多种方式创建实例。 从 .NET Framework 4.5 开始，最常见的方法是调用静态 `Run` 方法。 Run方法提供了一种简单的方法来使用默认值启动任务，而无需其他参数。

**例如，**

```
using System;
using System.Threading.Tasks;
public class Example
{
 public static async Task Main()
 {
 await Task.Run( () => {
 int ctr = 0;
 for (ctr = 0; ctr <= 1000000; ctr++)
 {}
 Console.WriteLine("Finished {0} loop iterations",
 ctr);
 } );
 }
}
```

.NET Framework 4 中启动任务的最常见方法，是静态 [TaskFactory.StartNew](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.taskfactory.startnew?view=net-5.0) 方法。 [Task.Factory](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.factory?view=net-5.0)属性返回 [TaskFactory](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.taskfactory?view=net-5.0) 对象。 方法的重载 [TaskFactory.StartNew](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.taskfactory.startnew?view=net-5.0) 使你可以指定要传递给任务创建选项和任务计划程序的参数。

**例如，**

```
using System;
using System.Threading.Tasks;

public class Example
{
 public static void Main()
 {
 Task t = Task.Factory.StartNew( () => {
 int ctr = 0;
 for (ctr = 0; ctr <= 1000000; ctr++)
 {}
 Console.WriteLine("Finished {0} loop iterations",
 ctr);
 } );
 t.Wait();
 }
}
```

## 3、Thread和Task常用属性和方法

1）Thread属性



| 属性               | 描述                                                         |
| ------------------ | ------------------------------------------------------------ |
| CurrentContext     | 获取线程正在其中执行的当前上下文。                           |
| CurrentCulture     | 获取或设置当前线程的区域性。                                 |
| CurrentPrincipal   | 获取或设置线程的当前负责人（对基于角色的安全性而言）。       |
| CurrentThread      | 获取当前正在运行的线程。                                     |
| CurrentUICulture   | 获取或设置资源管理器使用的当前区域性以便在运行时查找区域性特定的资源。 |
| ExecutionContext   | 获取一个 ExecutionContext 对象，该对象包含有关当前线程的各种上下文的信息。 |
| IsAlive            | 获取一个值，该值指示当前线程的执行状态。                     |
| IsBackground       | 获取或设置一个值，该值指示某个线程是否为后台线程。           |
| IsThreadPoolThread | 获取一个值，该值指示线程是否属于托管线程池。                 |
| ManagedThreadId    | 获取当前托管线程的唯一标识符。                               |
| Name               | 获取或设置线程的名称。                                       |
| Priority           | 获取或设置一个值，该值指示线程的调度优先级。                 |
| ThreadState        | 获取一个值，该值包含当前线程的状态。                         |



2）Thread方法



| 方法                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Abort()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.abort?view=net-5.0#System_Threading_Thread_Abort) | 已过时。在调用此方法的线程上引发 [ThreadAbortException](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.threadabortexception?view=net-5.0)，以开始终止此线程的过程。 调用此方法通常会终止线程。 |
| [Abort(Object)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.abort?view=net-5.0#System_Threading_Thread_Abort_System_Object_) | 已过时。引发在其上调用的线程中的 [ThreadAbortException](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.threadabortexception?view=net-5.0) 以开始处理终止线程，同时提供有关线程终止的异常信息。 调用此方法通常会终止线程。 |
| [Finalize()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.finalize?view=net-5.0#System_Threading_Thread_Finalize) | 确保垃圾回收器回收 [Thread](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread?view=net-5.0) 对象时释放资源并执行其他清理操作。 |
| [GetCurrentProcessorId()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.getcurrentprocessorid?view=net-5.0#System_Threading_Thread_GetCurrentProcessorId) | 获取用于指示当前线程正在哪个处理器上执行的 ID。              |
| [GetData(LocalDataStoreSlot)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.getdata?view=net-5.0#System_Threading_Thread_GetData_System_LocalDataStoreSlot_) | 在当前线程的当前域中从当前线程上指定的槽中检索值。 为了获得更好的性能，请改用以 [ThreadStaticAttribute](https://docs.microsoft.com/zh-cn/dotnet/api/system.threadstaticattribute?view=net-5.0) 特性标记的字段。 |
| [GetDomain()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.getdomain?view=net-5.0#System_Threading_Thread_GetDomain) | 返回当前线程正在其中运行的当前域。                           |
| [GetDomainID()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.getdomainid?view=net-5.0#System_Threading_Thread_GetDomainID) | 返回唯一的应用程序域标识符。                                 |
| [GetNamedDataSlot(String)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.getnameddataslot?view=net-5.0#System_Threading_Thread_GetNamedDataSlot_System_String_) | 查找命名的数据槽。 为了获得更好的性能，请改用以 [ThreadStaticAttribute](https://docs.microsoft.com/zh-cn/dotnet/api/system.threadstaticattribute?view=net-5.0) 特性标记的字段。 |
| [Join()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.join?view=net-5.0#System_Threading_Thread_Join) | 在继续执行标准的 COM 和 SendMessage 消息泵处理期间，阻止调用线程，直到由该实例表示的线程终止。 |
| [Join(Int32)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.join?view=net-5.0#System_Threading_Thread_Join_System_Int32_) | 在继续执行标准的 COM 和 SendMessage 消息泵处理期间，阻止调用线程，直到由该实例表示的线程终止或经过了指定时间为止。 |
| [Join(TimeSpan)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.join?view=net-5.0#System_Threading_Thread_Join_System_TimeSpan_) | 在继续执行标准的 COM 和 SendMessage 消息泵处理期间，阻止调用线程，直到由该实例表示的线程终止或经过了指定时间为止。 |
| [ResetAbort()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.resetabort?view=net-5.0#System_Threading_Thread_ResetAbort) | 取消当前线程所请求的 [Abort(Object)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.abort?view=net-5.0#System_Threading_Thread_Abort_System_Object_)。 |
| [Resume()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.resume?view=net-5.0#System_Threading_Thread_Resume) | 已过时。继续已挂起的线程。                                   |
| [SetApartmentState(ApartmentState)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.setapartmentstate?view=net-5.0#System_Threading_Thread_SetApartmentState_System_Threading_ApartmentState_) | 在线程启动前设置其单元状态。                                 |
| [SetCompressedStack(CompressedStack)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.setcompressedstack?view=net-5.0#System_Threading_Thread_SetCompressedStack_System_Threading_CompressedStack_) | 已过时。将捕获的 [CompressedStack](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.compressedstack?view=net-5.0) 应用到当前线程。 |
| [SetData(LocalDataStoreSlot, Object)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.setdata?view=net-5.0#System_Threading_Thread_SetData_System_LocalDataStoreSlot_System_Object_) | 在当前正在运行的线程上为此线程的当前域在指定槽中设置数据。 为了提高性能，请改用用 [ThreadStaticAttribute](https://docs.microsoft.com/zh-cn/dotnet/api/system.threadstaticattribute?view=net-5.0) 属性标记的字段。 |
| [Sleep(Int32)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.sleep?view=net-5.0#System_Threading_Thread_Sleep_System_Int32_) | 将当前线程挂起指定的毫秒数。                                 |
| [Sleep(TimeSpan)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.sleep?view=net-5.0#System_Threading_Thread_Sleep_System_TimeSpan_) | 将当前线程挂起指定的时间。                                   |
| [SpinWait(Int32)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.spinwait?view=net-5.0#System_Threading_Thread_SpinWait_System_Int32_) | 导致线程等待由 iterations 参数定义的时间量。                 |
| [Start()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.start?view=net-5.0#System_Threading_Thread_Start) | 导致操作系统将当前实例的状态更改为 [Running](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.threadstate?view=net-5.0#System_Threading_ThreadState_Running)。 |
| [Start(Object)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.start?view=net-5.0#System_Threading_Thread_Start_System_Object_) | 导致操作系统将当前实例的状态更改为 [Running](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.threadstate?view=net-5.0#System_Threading_ThreadState_Running)，并选择提供包含线程执行的方法要使用的数据的对象。 |
| [Suspend()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.thread.suspend?view=net-5.0#System_Threading_Thread_Suspend) | 已过时。挂起线程，或者如果线程已挂起，则不起作用。           |



3）Task属性



| 属性                                                         | 描述                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [AsyncState](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.asyncstate?view=net-5.0#System_Threading_Tasks_Task_AsyncState) | 获取在创建 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 时提供的状态对象，如果未提供，则为 null。 |
| [CompletedTask](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.completedtask?view=net-5.0#System_Threading_Tasks_Task_CompletedTask) | 获取一个已成功完成的任务。                                   |
| [CreationOptions](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.creationoptions?view=net-5.0#System_Threading_Tasks_Task_CreationOptions) | 获取用于创建此任务的 [TaskCreationOptions](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.taskcreationoptions?view=net-5.0)。 |
| [CurrentId](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.currentid?view=net-5.0#System_Threading_Tasks_Task_CurrentId) | 返回当前正在执行 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 的 ID。 |
| [Exception](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.exception?view=net-5.0#System_Threading_Tasks_Task_Exception) | 获取导致 [AggregateException](https://docs.microsoft.com/zh-cn/dotnet/api/system.aggregateexception?view=net-5.0) 提前结束的 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0)。 如果 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 成功完成或尚未引发任何异常，这将返回 null。 |
| [Factory](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.factory?view=net-5.0#System_Threading_Tasks_Task_Factory) | 提供对用于创建和配置 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 和 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task-1?view=net-5.0) 实例的工厂方法的访问。 |
| [Id](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.id?view=net-5.0#System_Threading_Tasks_Task_Id) | 获取此 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 实例的 ID。 |
| [IsCanceled](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.iscanceled?view=net-5.0#System_Threading_Tasks_Task_IsCanceled) | 获取此 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 实例是否由于被取消的原因而已完成执行。 |
| [IsCompleted](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.iscompleted?view=net-5.0#System_Threading_Tasks_Task_IsCompleted) | 获取一个值，它表示是否已完成任务。                           |
| [IsCompletedSuccessfully](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.iscompletedsuccessfully?view=net-5.0#System_Threading_Tasks_Task_IsCompletedSuccessfully) | 了解任务是否运行到完成。                                     |
| [IsFaulted](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.isfaulted?view=net-5.0#System_Threading_Tasks_Task_IsFaulted) | 获取 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 是否由于未经处理异常的原因而完成。 |
| [Status](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.status?view=net-5.0#System_Threading_Tasks_Task_Status) | 获取此任务的 [TaskStatus](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.taskstatus?view=net-5.0)。 |



4）Task方法



| [ConfigureAwait(Boolean)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.configureawait?view=net-5.0#System_Threading_Tasks_Task_ConfigureAwait_System_Boolean_) | 配置用于等待此 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0)的 awaiter。 |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [ContinueWith(Action, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_)[Object)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时接收调用方提供的状态信息并执行的延续任务。 |
| [ContinueWith(Action, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_CancellationToken_)[Object, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_CancellationToken_)[CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_CancellationToken_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时接收调用方提供的状态信息和取消标记，并以异步方式执行的延续任务。 |
| [ContinueWith(Action, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[Object, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[CancellationToken, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[TaskContinuationOptions, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[TaskScheduler)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时接收调用方提供的状态信息和取消标记并执行的延续任务。 延续任务根据一组指定的条件执行，并使用指定的计划程序。 |
| [ContinueWith(Action, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_Tasks_TaskContinuationOptions_)[Object, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_Tasks_TaskContinuationOptions_)[TaskContinuationOptions)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_Tasks_TaskContinuationOptions_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时接收调用方提供的状态信息并执行的延续任务。 延续任务根据一组指定的条件执行。 |
| [ContinueWith(Action, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_Tasks_TaskScheduler_)[Object, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_Tasks_TaskScheduler_)[TaskScheduler)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task_System_Object__System_Object_System_Threading_Tasks_TaskScheduler_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时接收调用方提供的状态信息并以异步方式执行的延续任务。 延续任务使用指定计划程序。 |
| [ContinueWith(Action)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task__) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时异步执行的延续任务。 |
| [ContinueWith(Action, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task__System_Threading_CancellationToken_)[CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task__System_Threading_CancellationToken_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时可接收取消标记并以异步方式执行的延续任务。 |
| [ContinueWith(Action, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task__System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[CancellationToken, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task__System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[TaskContinuationOptions, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task__System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[TaskScheduler)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task__System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_) | 创建一个在目标任务完成时按照指定的 [TaskContinuationOptions](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.taskcontinuationoptions?view=net-5.0) 执行的延续任务。 延续任务会收到一个取消标记，并使用指定的计划程序。 |
| [ContinueWith(Action, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task__System_Threading_Tasks_TaskContinuationOptions_)[TaskContinuationOptions)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task__System_Threading_Tasks_TaskContinuationOptions_) | 创建一个在目标任务完成时按照指定的 [TaskContinuationOptions](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.taskcontinuationoptions?view=net-5.0) 执行的延续任务。 |
| [ContinueWith(Action, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task__System_Threading_Tasks_TaskScheduler_)[TaskScheduler)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith_System_Action_System_Threading_Tasks_Task__System_Threading_Tasks_TaskScheduler_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时异步执行的延续任务。 延续任务使用指定计划程序。 |
| [ContinueWith(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_)[Func,](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_)[ Object)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成并返回一个值时接收调用方提供的状态信息并以异步方式执行的延续任务。 |
| [ContinueWith(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_CancellationToken_)[Func, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_CancellationToken_)[Object, CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_CancellationToken_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时异步执行并返回一个值的延续任务。 延续任务接收调用方提供的状态信息和取消标记。 |
| [ContinueWith(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[Func, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[Object, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[CancellationToken, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[TaskContinuationOptions, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[TaskScheduler)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成并返回一个值时根据指定的任务延续选项执行的延续任务。 延续任务接收调用方提供的状态信息和取消标记，并使用指定的计划程序。 |
| [ContinueWith(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_Tasks_TaskContinuationOptions_)[Func, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_Tasks_TaskContinuationOptions_)[Object, TaskContinuationOptions)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_Tasks_TaskContinuationOptions_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时根据指定的任务延续选项执行的延续任务。 延续任务接收调用方提供的状态信息。 |
| [ContinueWith(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_Tasks_TaskScheduler_)[Func,](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_Tasks_TaskScheduler_)[ Object, TaskScheduler)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task_System_Object___0__System_Object_System_Threading_Tasks_TaskScheduler_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时异步执行的延续任务。 延续任务接收调用方提供的状态信息，并使用指定的计划程序。 |
| [ContinueWith(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__)[Func)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task-1?view=net-5.0) 完成时异步执行并返回一个值的延续任务。 |
| [ContinueWith(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_CancellationToken_)[Func, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_CancellationToken_)[CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_CancellationToken_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时异步执行并返回一个值的延续任务。 延续任务收到取消标记。 |
| [ContinueWith(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[Func, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[CancellationToken, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[TaskContinuationOptions, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_)[TaskScheduler)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_CancellationToken_System_Threading_Tasks_TaskContinuationOptions_System_Threading_Tasks_TaskScheduler_) | 创建一个按照指定延续任务选项执行并返回一个值的延续任务。 延续任务被传入一个取消标记，并使用指定的计划程序。 |
| [ContinueWith(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_Tasks_TaskContinuationOptions_)[Func, T](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_Tasks_TaskContinuationOptions_)[askContinuationOptions)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_Tasks_TaskContinuationOptions_) | 创建一个按照指定延续任务选项执行并返回一个值的延续任务。     |
| [ContinueWith(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_Tasks_TaskScheduler_)[Func, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_Tasks_TaskScheduler_)[TaskScheduler)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.continuewith?view=net-5.0#System_Threading_Tasks_Task_ContinueWith__1_System_Func_System_Threading_Tasks_Task___0__System_Threading_Tasks_TaskScheduler_) | 创建一个在目标 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成时异步执行并返回一个值的延续任务。 延续任务使用指定计划程序。 |
| [Delay(Int32)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.delay?view=net-5.0#System_Threading_Tasks_Task_Delay_System_Int32_) | 创建一个在指定的毫秒数后完成的任务。                         |
| [Delay(Int32, CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.delay?view=net-5.0#System_Threading_Tasks_Task_Delay_System_Int32_System_Threading_CancellationToken_) | 创建一个在指定的毫秒数后完成的可取消任务。                   |
| [Delay(TimeSpan)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.delay?view=net-5.0#System_Threading_Tasks_Task_Delay_System_TimeSpan_) | 创建一个在指定的时间间隔后完成的任务。                       |
| [Delay(TimeSpan, CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.delay?view=net-5.0#System_Threading_Tasks_Task_Delay_System_TimeSpan_System_Threading_CancellationToken_) | 创建一个在指定的时间间隔后完成的可取消任务。                 |
| [Dispose()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.dispose?view=net-5.0#System_Threading_Tasks_Task_Dispose) | 释放 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 类的当前实例所使用的所有资源。 |
| [Dispose(Boolean)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.dispose?view=net-5.0#System_Threading_Tasks_Task_Dispose_System_Boolean_) | 释放 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0)，同时释放其所有非托管资源。 |
| [FromCanceled(CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.fromcanceled?view=net-5.0#System_Threading_Tasks_Task_FromCanceled_System_Threading_CancellationToken_) | 创建 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0)，它因指定的取消标记进行的取消操作而完成。 |
| [FromCanceled(CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.fromcanceled?view=net-5.0#System_Threading_Tasks_Task_FromCanceled__1_System_Threading_CancellationToken_) | 创建 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task-1?view=net-5.0)，它因指定的取消标记进行的取消操作而完成。 |
| [FromException(Exception)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.fromexception?view=net-5.0#System_Threading_Tasks_Task_FromException_System_Exception_) | 创建 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0)，它在完成后出现指定的异常。 |
| [FromException(Exception)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.fromexception?view=net-5.0#System_Threading_Tasks_Task_FromException__1_System_Exception_) | 创建 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task-1?view=net-5.0)，它在完成后出现指定的异常。 |
| [FromResult(TResult)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.fromresult?view=net-5.0#System_Threading_Tasks_Task_FromResult__1___0_) | 创建指定结果的、成功完成的 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task-1?view=net-5.0)。 |
| [GetAwaiter()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.getawaiter?view=net-5.0#System_Threading_Tasks_Task_GetAwaiter) | 获取用于等待此 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 的 awaiter。 |
| [Run(Action)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.run?view=net-5.0#System_Threading_Tasks_Task_Run_System_Action_) | 将在线程池上运行的指定工作排队，并返回代表该工作的 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象。 |
| [Run(Action, CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.run?view=net-5.0#System_Threading_Tasks_Task_Run_System_Action_System_Threading_CancellationToken_) | 将在线程池上运行的指定工作排队，并返回代表该工作的 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象。 可使用取消标记来取消工作（如果尚未启动）。 |
| [Run(Func)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.run?view=net-5.0#System_Threading_Tasks_Task_Run_System_Func_System_Threading_Tasks_Task__) | 将在线程池上运行的指定工作排队，并返回 function 所返回的任务的代理项。 |
| [Run(Func, CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.run?view=net-5.0#System_Threading_Tasks_Task_Run_System_Func_System_Threading_Tasks_Task__System_Threading_CancellationToken_) | 将在线程池上运行的指定工作排队，并返回 function 所返回的任务的代理项。 可使用取消标记来取消工作（如果尚未启动）。 |
| [Run(Func>)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.run?view=net-5.0#System_Threading_Tasks_Task_Run__1_System_Func_System_Threading_Tasks_Task___0___) | 将指定的工作排成队列在线程池上运行，并返回由 function 返回的 Task(TResult) 的代理。 可使用取消标记来取消工作（如果尚未启动）。 |
| [Run(Func>, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.run?view=net-5.0#System_Threading_Tasks_Task_Run__1_System_Func_System_Threading_Tasks_Task___0___System_Threading_CancellationToken_)[CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.run?view=net-5.0#System_Threading_Tasks_Task_Run__1_System_Func_System_Threading_Tasks_Task___0___System_Threading_CancellationToken_) | 将指定的工作排成队列在线程池上运行，并返回由 function 返回的 Task(TResult) 的代理。 |
| [Run(Func)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.run?view=net-5.0#System_Threading_Tasks_Task_Run__1_System_Func___0__) | 将在线程池上运行的指定工作排队，并返回代表该工作的 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task-1?view=net-5.0) 对象。 可使用取消标记来取消工作（如果尚未启动）。 |
| [Run(Func, ](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.run?view=net-5.0#System_Threading_Tasks_Task_Run__1_System_Func___0__System_Threading_CancellationToken_)[CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.run?view=net-5.0#System_Threading_Tasks_Task_Run__1_System_Func___0__System_Threading_CancellationToken_) | 将在线程池上运行的指定工作排队，并返回代表该工作的 Task(TResult) 对象。 |
| [RunSynchronously()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.runsynchronously?view=net-5.0#System_Threading_Tasks_Task_RunSynchronously) | 对当前的 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 同步运行 [TaskScheduler](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.taskscheduler?view=net-5.0)。 |
| [RunSynchronously(TaskScheduler)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.runsynchronously?view=net-5.0#System_Threading_Tasks_Task_RunSynchronously_System_Threading_Tasks_TaskScheduler_) | 对提供的 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 同步运行 [TaskScheduler](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.taskscheduler?view=net-5.0)。 |
| [Start()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.start?view=net-5.0#System_Threading_Tasks_Task_Start) | 启动 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0)，并将它安排到当前的 [TaskScheduler](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.taskscheduler?view=net-5.0) 中执行。 |
| [Start(TaskScheduler)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.start?view=net-5.0#System_Threading_Tasks_Task_Start_System_Threading_Tasks_TaskScheduler_) | 启动 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0)，并将它安排到指定的 [TaskScheduler](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.taskscheduler?view=net-5.0) 中执行。 |
| [ToString()](https://docs.microsoft.com/zh-cn/dotnet/api/system.object.tostring?view=net-5.0#System_Object_ToString) | 返回表示当前对象的字符串。(继承自 [Object](https://docs.microsoft.com/zh-cn/dotnet/api/system.object?view=net-5.0)) |
| [Wait()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.wait?view=net-5.0#System_Threading_Tasks_Task_Wait) | 等待 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成执行过程。 |
| [Wait(CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.wait?view=net-5.0#System_Threading_Tasks_Task_Wait_System_Threading_CancellationToken_) | 等待 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成执行过程。 如果在任务完成之前取消标记已取消，等待将终止。 |
| [Wait(Int32)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.wait?view=net-5.0#System_Threading_Tasks_Task_Wait_System_Int32_) | 等待 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 在指定的毫秒数内完成执行。 |
| [Wait(Int32, CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.wait?view=net-5.0#System_Threading_Tasks_Task_Wait_System_Int32_System_Threading_CancellationToken_) | 等待 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 完成执行过程。 如果在任务完成之前超时间隔结束或取消标记已取消，等待将终止。 |
| [Wait(TimeSpan)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.wait?view=net-5.0#System_Threading_Tasks_Task_Wait_System_TimeSpan_) | 等待 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 在指定的时间间隔内完成执行。 |
| [WaitAll(Task[\])](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.waitall?view=net-5.0#System_Threading_Tasks_Task_WaitAll_System_Threading_Tasks_Task___) | 等待提供的所有 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象完成执行过程。 |
| [WaitAll(Task[\], CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.waitall?view=net-5.0#System_Threading_Tasks_Task_WaitAll_System_Threading_Tasks_Task___System_Threading_CancellationToken_) | 等待提供的所有 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象完成执行过程（除非取消等待）。 |
| [WaitAll(Task[\], Int32)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.waitall?view=net-5.0#System_Threading_Tasks_Task_WaitAll_System_Threading_Tasks_Task___System_Int32_) | 等待所有提供的 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 在指定的毫秒数内完成执行。 |
| [WaitAll(Task[\], Int32, CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.waitall?view=net-5.0#System_Threading_Tasks_Task_WaitAll_System_Threading_Tasks_Task___System_Int32_System_Threading_CancellationToken_) | 等待提供的所有 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象在指定的毫秒数内完成执行，或等到取消等待。 |
| [WaitAll(Task[\], TimeSpan)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.waitall?view=net-5.0#System_Threading_Tasks_Task_WaitAll_System_Threading_Tasks_Task___System_TimeSpan_) | 等待所有提供的可取消 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象在指定的时间间隔内完成执行。 |
| [WaitAny(Task[\])](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.waitany?view=net-5.0#System_Threading_Tasks_Task_WaitAny_System_Threading_Tasks_Task___) | 等待提供的任一 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象完成执行过程。 |
| [WaitAny(Task[\], CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.waitany?view=net-5.0#System_Threading_Tasks_Task_WaitAny_System_Threading_Tasks_Task___System_Threading_CancellationToken_) | 等待提供的任何 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象完成执行过程（除非取消等待）。 |
| [WaitAny(Task[\], Int32)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.waitany?view=net-5.0#System_Threading_Tasks_Task_WaitAny_System_Threading_Tasks_Task___System_Int32_) | 等待任何提供的 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象在指定的毫秒数内完成执行。 |
| [WaitAny(Task[\], Int32, CancellationToken)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.waitany?view=net-5.0#System_Threading_Tasks_Task_WaitAny_System_Threading_Tasks_Task___System_Int32_System_Threading_CancellationToken_) | 等待提供的任何 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象在指定的毫秒数内完成执行，或等到取消标记取消。 |
| [WaitAny(Task[\], TimeSpan)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.waitany?view=net-5.0#System_Threading_Tasks_Task_WaitAny_System_Threading_Tasks_Task___System_TimeSpan_) | 等待任何提供的 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象在指定的时间间隔内完成执行。 |
| [WhenAll(IEnumerable)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenall?view=net-5.0#System_Threading_Tasks_Task_WhenAll_System_Collections_Generic_IEnumerable_System_Threading_Tasks_Task__) | 创建一个任务，该任务将在可枚举集合中的所有 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象都已完成时完成。 |
| [WhenAll(Task[\])](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenall?view=net-5.0#System_Threading_Tasks_Task_WhenAll_System_Threading_Tasks_Task___) | 创建一个任务，该任务将在数组中的所有 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task?view=net-5.0) 对象都已完成时完成。 |
| [WhenAll(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenall?view=net-5.0#System_Threading_Tasks_Task_WhenAll__1_System_Collections_Generic_IEnumerable_System_Threading_Tasks_Task___0___)[IEnumerable>)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenall?view=net-5.0#System_Threading_Tasks_Task_WhenAll__1_System_Collections_Generic_IEnumerable_System_Threading_Tasks_Task___0___) | 创建一个任务，该任务将在可枚举集合中的所有 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task-1?view=net-5.0) 对象都已完成时完成。 |
| [WhenAll(Task[\])](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenall?view=net-5.0#System_Threading_Tasks_Task_WhenAll__1_System_Threading_Tasks_Task___0____) | 创建一个任务，该任务将在数组中的所有 [Task](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task-1?view=net-5.0) 对象都已完成时完成。 |
| [WhenAny(IEnumerable)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenany?view=net-5.0#System_Threading_Tasks_Task_WhenAny_System_Collections_Generic_IEnumerable_System_Threading_Tasks_Task__) | 任何提供的任务已完成时，创建将完成的任务。                   |
| [WhenAny(Task, Task)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenany?view=net-5.0#System_Threading_Tasks_Task_WhenAny_System_Threading_Tasks_Task_System_Threading_Tasks_Task_) | 提供的任一任务完成时，创建将完成的任务。                     |
| [WhenAny(Task[\])](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenany?view=net-5.0#System_Threading_Tasks_Task_WhenAny_System_Threading_Tasks_Task___) | 任何提供的任务已完成时，创建将完成的任务。                   |
| [WhenAny(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenany?view=net-5.0#System_Threading_Tasks_Task_WhenAny__1_System_Collections_Generic_IEnumerable_System_Threading_Tasks_Task___0___)[IEnumerable>)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenany?view=net-5.0#System_Threading_Tasks_Task_WhenAny__1_System_Collections_Generic_IEnumerable_System_Threading_Tasks_Task___0___) | 任何提供的任务已完成时，创建将完成的任务。                   |
| [WhenAny(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenany?view=net-5.0#System_Threading_Tasks_Task_WhenAny__1_System_Threading_Tasks_Task___0__System_Threading_Tasks_Task___0__)[Task, Task)](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenany?view=net-5.0#System_Threading_Tasks_Task_WhenAny__1_System_Threading_Tasks_Task___0__System_Threading_Tasks_Task___0__) | 提供的任一任务完成时，创建将完成的任务。                     |
| [WhenAny(](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenany?view=net-5.0#System_Threading_Tasks_Task_WhenAny__1_System_Threading_Tasks_Task___0____)[Task[\])](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.whenany?view=net-5.0#System_Threading_Tasks_Task_WhenAny__1_System_Threading_Tasks_Task___0____) | 任何提供的任务已完成时，创建将完成的任务。                   |
| [Yield()](https://docs.microsoft.com/zh-cn/dotnet/api/system.threading.tasks.task.yield?view=net-5.0#System_Threading_Tasks_Task_Yield) | 创建异步产生当前上下文的等待任务。                           |

## 4、多线程的优缺点及使用场景





使用多线程可以提高CPU的利用率。在多线程程序中，一个线程必须等待的时候，CPU可以运行其它的线程而不是等待，这样就大大提高了程序的效率。线程也是程序，所以线程需要占用内存，线程越多占用内存也越多； 

多线程需要协调和管理，所以需要CPU时间跟踪线程； 线程之间对共享资源的访问会相互影响，必须解决竞用共享资源的问题；线程太多会导致控制太复杂，最终可能造成很多Bug。拥有多线程本身并不复杂，复杂是的线程的交互作用，这带来了无论是否交互是否是有意的，都会带来较长的开发周期，以及带来间歇性和非重复性的Bug。因此，要么多线程的交互设计简单一些，要么就根本不使用多线程。

当用户频繁地分配和切换线程时，多线程会带来增加资源和CPU的开销。在某些情况下，太多的I/O操作是非常棘手的，当只有一个或两个工作线程要比有较多的线程在相同时间执行任务块的多。

使用多线程的任务应具有并发性，即任务可以拆分为多个子任务，并发执行。只有在CPU是性能瓶颈的情况下，多线程才能实现提升性能的目的。如一段程序，瓶颈在于IO操作，即使把这个程序拆分到2个线程中执行，也是无法提升性能的，另外，CPU必须是多核的。

**相关文档：**[.NET(C#) Thread、Task或Parallel实现多线程的使用总结](https://www.cjavapy.com/article/2499/)