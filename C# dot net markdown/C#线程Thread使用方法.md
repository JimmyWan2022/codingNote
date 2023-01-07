```c#
public class MyThread
{
    private Thread thread;
    private volatile bool isStopped = false;

    public void Start()
    {
        thread = new Thread(DoWork);
        thread.Start();
    }

    public void Stop()
    {
        isStopped = true;
        thread.Join();
    }

    private void DoWork()
    {
        while (!isStopped)
        {
            // 线程的工作逻辑

            // 检查线程是否被中断
            if (thread.ThreadState == ThreadState.AbortRequested)
            {
                // 如果线程被中断，则退出循环
                break;
            }
        }
    }
}

```

