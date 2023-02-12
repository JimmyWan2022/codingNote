docker官网：https://www.docker.com/

docker hub仓库：https://hub.docker.com/



windows安装docker 环境

1.安装linux虚拟机

https://learn.microsoft.com/zh-cn/windows/wsl/install-manual#step-4---download-the-linux-kernel-update-package

2.下载windows docker 桌面版本



本地linux服务器账户密码

jimmywan

123456



## linux安装教程

1.国内linux安装docker engine

2.修改为国内dokcer镜像，阿里云镜像

3.更新yum软件包索引 

```
yum makecache fast
```

4.安装docker engine



后台启动docker

```
systemctl start docker
```

查看后台正在运行的程序

```
ps - ef|grep docker
```

**阿里云镜像加速，可以白嫖**

https://developer.aliyun.com/article/1131834

![image-20230212111110747](docker%20%E7%AC%94%E8%AE%B0.assets/image-20230212111110747.png)

![image-20230212112313991](docker%20%E7%AC%94%E8%AE%B0.assets/image-20230212112313991.png)

![image-20230212112642167](docker%20%E7%AC%94%E8%AE%B0.assets/image-20230212112642167.png)

https://www.youtube.com/watch?v=C0WbUzPALZI&list=PLmOn9nNkQxJFtOGw9fsoLHgtCxcki7TtK&index=18 看到第18集