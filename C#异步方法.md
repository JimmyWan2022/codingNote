```c#
public async Task sendReSelfTestCommandAsync(string address)
        {
            ReSelfTestFlag = false;
            // 重新自检
            protocolService.setAddress(address);
            Boolean flag1 = protocolService.sendReSelfTestCommand();
            await Task.Delay(1000);
            if (ReSelfTestFlag)
            {
                printText("------重新自检,成功---------");
            }
            else {
                printText("------重新自检,失败---------");
            }
    }
```



​        

