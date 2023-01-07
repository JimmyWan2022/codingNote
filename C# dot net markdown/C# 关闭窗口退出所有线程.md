##  C# 使用多线程，关闭窗体时，退出所有线程

- this.Close();   只是关闭当前窗口，若不是主窗体的话，是无法退出程序的，另外若有托管线程（非主线程），也无法干净地退出；
- Application.Exit();  强制所有消息中止，退出所有的窗体，但是若有托管线程（非主线程），也无法干净地退出；
- Application.ExitThread(); 强制中止调用线程上的所有消息，同样面临其它线程无法正确退出的问题；
- System.Environment.Exit(0);   这是最彻底的退出方式，不管什么线程都被强制退出，把程序结束的很干净。

```c#
// 窗口关闭事件
 private void FormHome1_FormClosing_1(object sender, FormClosingEventArgs e)
        {
            try
            {
                exit();
            }
            catch (Exception err)
            {
            }
        }
```



```c#

 public void exit()
        {
            try
            {
                //负载板断电
                loadBoardUncharge();
                serialPortSendMessage("OUTP 0");
                //关闭监听线程
                closeListen();
                serialPort_listen = false;
                receiveMessageFlag = false;
                //关闭线程
                if (serialPortThread != null && serialPortThread.IsAlive)
                {
                    serialPortThread.Abort();
                    serialPortThread = null;
                }
                Application.Exit();
                System.Environment.Exit(0);
            }
            catch (Exception err)
            {
                Console.WriteLine(err);
            }
        }
```

