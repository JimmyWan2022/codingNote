##  ASP.NET Core中使用SmtpClient发送邮件的方法代码

本文主要介绍ASP .NET Core使用SmtpClient邮件的方法，从.NET Core 2.0开始，Microsoft引入了SmtpClient，与.NET Framework 4中的实现相同。并且不必依赖第三方Nuget包直接可以使用。

**1、SmtpClient配置文件**

由于配置已经注入`Startup.cs`构造函数，我们可以使用我们的配置文件来存储我们的`SmtpClient`配置。

```
{  
  "Logging": {  
    "IncludeScopes": false,  
    "LogLevel": {  
      "Default": "Debug",  
      "System": "Information",  
      "Microsoft": "Information"  
    }  
  },  
  "Email": {  
    "Smtp": {  
      "Host": "smtp.gmail.com",  
      "Port": 25,  
      "Username": "mail_username",  
      "Password": "mail_password"  
    }  
  }  
}  
```



**2、Scoped service方式配置使用**

在ASP.NET Core依赖注入上下文中，作用域指的是每个请求。为每个控制器实例提供SmtpClient实例。

1）Startup.cs中配置代码

```
public class Startup{
  public Startup(IConfiguration configuration)  
        {  
            Configuration = configuration;  
        }  
        public IConfiguration Configuration { get; }  
        // 此方法由运行时调用。使用此方法向容器添加service。  
        public void ConfigureServices(IServiceCollection services)  
        {  
            services.AddScoped<SmtpClient>((serviceProvider) =>  
            {  
                var config = serviceProvider.GetRequiredService<IConfiguration>();  
                return new SmtpClient()  
                {  
                    Host = config.GetValue<String>("Email:Smtp:Host"),  
                    Port = config.GetValue<int>("Email:Smtp:Port"),  
                    Credentials = new NetworkCredential(  
                            config.GetValue<String>("Email:Smtp:Username"),   
                            config.GetValue<String>("Email:Smtp:Password")  
                        )  
                };  
            });  
            services.AddMvc();  
        }  
        // 此方法由运行时调用。使用此方法配置HTTP请求管道。  
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)  
        {  
            if (env.IsDevelopment())  
            {  
                app.UseDeveloperExceptionPage();  
            }  
            app.UseMvc();  
        }  
    }  
```









2）Controller中调用代码

控制器中我们需要创建一个接受SmtpClient作为参数的构造函数。此构造函数将配置从依赖项注入上下文中可用的SmtpClient实例，并且它将进一步可用于控制器中的所有方法。

```
[Route("api/[controller]")]  
public class MailController : Controller  
{  
    private SmtpClient smtpClient;  
    public MailController(SmtpClient smtpClient)  
    {  
        this.smtpClient = smtpClient;  
    }  
    [HttpPost]  
    public async Task<IActionResult> Post()  
    {  
        await this.smtpClient.SendMailAsync(new MailMessage(  
            to: "sample1.app@noname.test",  
            subject: "Test message subject",  
            body: "Test message body"  
            ));  
        return Ok("OK");  
    }  
    protected override void Dispose(bool disposing)  
    {  
        this.smtpClient.Dispose();  
        base.Dispose(disposing);  
    }  
}  
```













**3、Transient service方式配置使用**

这种方法基本上是按需进行依赖注入。当您需要配置的SmtpClient时，您要求解析器为您提供一个实例。通过在ConfigureServices方法中对作用域进行少量更改，Startup.cs代码几乎保持不变。

1）Startup.cs中配置代码

```
public class Startup  
{  
    public Startup(IConfiguration configuration)  
    {  
        Configuration = configuration;  
    }  
    public IConfiguration Configuration { get; }  
    // 此方法由运行时调用。使用此方法向容器添加service。 
    public void ConfigureServices(IServiceCollection services)  
    {  
        services.AddTransient<SmtpClient>((serviceProvider) =>  
        {  
            var config = serviceProvider.GetRequiredService<IConfiguration>();  
            return new SmtpClient()  
            {  
                Host = config.GetValue<String>("Email:Smtp:Host"),  
                Port = config.GetValue<int>("Email:Smtp:Port"),  
                Credentials = new NetworkCredential(  
                        config.GetValue<String>("Email:Smtp:Username"),   
                        config.GetValue<String>("Email:Smtp:Password")  
                    )  
            };  
        });  
        services.AddMvc();  
    }  
    // 此方法由运行时调用。使用此方法配置HTTP请求管道。
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)  
    {  
        if (env.IsDevelopment())  
        {  
            app.UseDeveloperExceptionPage();  
        }  
        app.UseMvc();  
    }  
} 
```









2）Controller中调用代码

在控制器上，实例不是在控制器构造函数上创建的，而是在使用它的方法内部创建的。

```
[Route("api/[controller]")]  
public class MailController : Controller  
{  
    [HttpPost]  
    public async Task<IActionResult> Post()  
    {  
        using (var smtpClient = HttpContext.RequestServices.GetRequiredService<SmtpClient>())  
        {  
            await smtpClient.SendMailAsync(new MailMessage(  
                   to: "sample1.app@noname.test",  
                   subject: "Test message subject",  
                   body: "Test message body"  
                   ));  
            return Ok("OK");  
        }  
    }  
}  
```