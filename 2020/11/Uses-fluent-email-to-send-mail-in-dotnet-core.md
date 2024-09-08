---
title: .NET Core使用FluentEmail发送邮件
slug: Uses-fluent-email-to-send-mail-in-dotnet-core
description: 在实际的项目开发中，我们会遇到许多需要通过程序发送邮件的场景，比如异常报警、消息、进度通知等等。
date: 2020-11-28 09:18:07
copyright: Reprinted
author: yi念之间
originalTitle: .NET Core使用FluentEmail发送邮件
originalLink: https://www.cnblogs.com/wucy/p/13797578.html
draft: False
cover: https://img1.dotnet9.com/2020/11/cover_03.jpeg
categories: .NET
tags: Web API,FluentEmail,邮件发送
---

## 前言

在实际的项目开发中，我们会遇到许多需要通过程序发送邮件的场景，比如异常报警、消息、进度通知等等。一般情况下我们使用原生的 SmtpClient 类库居多，它能满足我们绝大多数场景。但是使用起来不够简洁，许多场景需要我们自行封装方法去实现，而且代码量非常可观。庆幸的是，我们有一款非常棒的组件，能满足我们绝大多数应用场景，而且使用简单功能强大，就是我们今天要说的 FluentEmail，这也是我们实际在项目中正在使用的邮件发送组件。如果你们在.Net Core 中有发送邮件的需求，也推荐去尝试一下。

## FluentEmail

FluentEmail 是一款在 GitHub 上开源免费的支持.Net 和.Net Core 邮件发送组件，目前已有 1K 多的 Star，而且近两年随着.Net Core 的日益成熟，它的 Star 增长趋势还是非常迅猛的。它在 GitHub 地址是https://github.com/lukencode/FluentEmail，它的功能非常强大而且非常实用，支持Razor的邮件模板和支持使用SendGrid，MailGun，SMTP发送邮件，而且使用也非常简单。

## Nuget 组件

FluentEmail 功能强大，而且对不同场景的支持都有独立的 Nuget 包，这种低耦合的拆分不仅使得依赖非常清晰，而且避免引入不需要的代码，具体功能包含在以下的组件包中

- [FluentEmail.Core](https://github.com/lukencode/FluentEmail/tree/master/src/FluentEmail.Core) - 基础核心包，包含了基础的模型定义和默认的设置，而且以下的引用包都包含了这个核心包。
- [FluentEmail.Smtp](https://github.com/lukencode/FluentEmail/blob/master/src/Senders/FluentEmail.Smtp) - 使用 SMTP 服务发送邮件的程序包。
- [FluentEmail.Razor](https://github.com/lukencode/FluentEmail/blob/master/src/Renderers/FluentEmail.Razor) - 通过 Razor 模板生成邮件发送内容。
- [FluentEmail.Mailgun](https://github.com/lukencode/FluentEmail/blob/master/src/Senders/FluentEmail.Mailgun) - 使用 Mailgun 的 Rest 接口发送邮件。
- [FluentEmail.SendGrid](https://github.com/lukencode/FluentEmail/blob/master/src/Senders/FluentEmail.SendGrid) - 使用 SendGrid 接口发送邮件。
- [FluentEmail.Mailtrap](https://github.com/lukencode/FluentEmail/blob/master/src/Senders/FluentEmail.Mailtrap) - 发送邮件 Mailtrap, 使用的是 FluentEmail.Smtp 包进行发送.
- [FluentEmail.MailKit](https://github.com/lukencode/FluentEmail/blob/master/src/Senders/FluentEmail.MailKit) - 使用 MailKit 邮件库发送邮件。

## 普通邮件方式

接下来我们就演示一下如何使用 FluentEmail 发送邮件，由于我们实际业务中大多数都使用的 SMTP 的方式发送邮件，所以我们就以此为做演示，首先我们在项目中引入 FluentEmail.Smtp 包，目前最新版本为 2.8.0

```xml
<PackageReference Include="FluentEmail.Smtp" Version="2.8.0" />
```

接下来我们就可以愉快的写代码了，它的编码使用方式非常简单而且非常简洁，主要通过链式编程的方式

```C#
//如果使用smtp服务发送邮件必须要设置smtp服务信息
SmtpClient smtp = new SmtpClient
{
    //smtp服务器地址(我这里以126邮箱为例，可以依据具体你使用的邮箱设置)
    Host = "smtp.126.com",
    UseDefaultCredentials = true,
    DeliveryMethod = SmtpDeliveryMethod.Network,
    //这里输入你在发送smtp服务器的用户名和密码
    Credentials = new NetworkCredential("邮箱用户名", "邮箱密码")
};
//设置默认发送信息
Email.DefaultSender = new SmtpSender(smtp);
var email = Email
    //发送人
    .From("zhangsan@126.com")
    //收件人
    .To("lisi@qq.com")
    //抄送人
    .CC("admin@126.com")
    //邮件标题
    .Subject("邮件标题")
    //邮件内容
    .Body("邮件内容");
//依据发送结果判断是否发送成功
var result = email.Send();
//或使用异步的方式发送
//await email.SendAsync();
if (result.Successful)
{
    //发送成功逻辑
}
else
{
    //发送失败可以通过result.ErrorMessages查看失败原因
}
```

如果你发送的内容中包含 html 格式的内容可以使用如下方式

```C#
var email = Email
    //发送人
    .From("zhangsan@126.com")
    //收件人
    .To("lisi@qq.com")
    //抄送人
    .CC("admin@126.com")
    //邮件标题
    .Subject("邮件标题")
    //只需要额外设置第二个参数为true即可
    .Body("<h1 align=\"center\">.NET大法好</h1><p>是的,这一点毛病都没有</p>",true);
//发送
var result = email.Send();
```

这个我们通过点击查看 Body 的方法声明即可得知第二个参数是用来表示内容是否为 html 格式，默认为 false

```C#
IFluentEmail Body (string body, bool isHtml = false);
```

如果邮件的收件人为多个邮箱地址的话,可以采用 To 方法的另一个重载方法可以接受 List<FluentEmail.Core.Models.Address>

```C#
var email = Email
    //发送人
    .From("zhangsan@126.com")
    //邮件标题
    .Subject("邮件标题")
    //邮件内容
    .Body("<h1 align=\"center\">.NET大法好</h1><p>是的,一点毛病都没有</p>",true);

//构建多个接收人邮箱
string toUserStr = "oldwang@126.com;xiaoming@163.com;xiaoli@qq.com";
List<FluentEmail.Core.Models.Address> toUsers = toUserStr.Split(";")
    .Select(i => new FluentEmail.Core.Models.Address { EmailAddress = i }).ToList();
//支持传入Address集合
email.To(toUsers)
//抄送人集合
.CC(toUsers);
//发送
var result = email.Send();
```

如果我们需要在发送的邮件中添加一个附件的话，可以使用 Attache 方法添加附件

```C#
var email = Email
        //发送人
        .From("zhangsan@qq.com")
        //收件人
        .To("lisi@126.com")
        //抄送人
        .CC("admin@126.com")
        //邮件标题
        .Subject("关于.Net Core怎么样")
        //邮件内容
        .Body("<h1 align=\"center\">.NET Core</h1><p>.Net Core很优秀吗？是的,一点毛病都没有！！！</p>",true);

//构建附件
var stream = new MemoryStream();
var sw = new StreamWriter(stream);
sw.WriteLine("您好,这是文本里的内容");
sw.Flush();
stream.Seek(0, SeekOrigin.Begin);
var attachment = new FluentEmail.Core.Models.Attachment
{
    Data = stream,
    ContentType = "text/plain",
    Filename = "Hello.txt"
};
//添加附件
email.Attach(attachment);
var result = email.Send();
```

如果需要添加多个附件的话 Attach 方法支持传入 Attachment 集合

```C#
//构建附件
var stream = new MemoryStream();
var sw = new StreamWriter(stream);
sw.WriteLine("您好,这是文本里的内容");
sw.Flush();
stream.Seek(0, SeekOrigin.Begin);
//附件1
var attachment = new FluentEmail.Core.Models.Attachment
{
    Data = stream,
    ContentType = "text/plain",
    Filename = "Hello.txt"
};

//附件2
var attachment2 = new FluentEmail.Core.Models.Attachment
{
    Data = File.OpenRead(@"D:\test.txt"),
    ContentType = "text/plain",
    Filename = "test.txt"
};

//添加附件
email.Attach(new List<FluentEmail.Core.Models.Attachment> { attachment, attachment2 });
var result = email.Send();
```

## 使用 Razor 模板

上面的内容我们介绍了使用 FluentEmail 使用常规的方式发送邮件，但是有时候我们需要发送一些内容是动态的或者发送一些样式比较复杂 html 网页内容。通常我们使用原生的 SmptClient 的时候都是通过拼接 html 代码方式，但是这种方式相对来说比较费时费力，对于.Net 程序员来说 Razor 引擎是我们构建动态 html 页面最熟悉的方式，而 FluentEmail 正是为我们提供了 Razor 模板的支持。首先，我们在之前的基础上引入 FluentEmail.Razor 模板支持组件

```xml
<PackageReference Include="FluentEmail.Razor" Version="2.8.0" />
```

由于 ASP.NET Core2.2 开始默认是使用的视图编译功能，视图会编译成 项目名称.Views.dll，但是 FluentEmail.Razor 又需要读取视图文件的内容，所以要在 csproj 文件中添加以下内容

```xml
<MvcRazorExcludeRefAssembliesFromPublish>true</MvcRazorExcludeRefAssembliesFromPublish>
```

然后我们就可以使用 Razor 模板生成邮件内容，具体的使用方式

```C#
//声明使用razor的方式
Email.DefaultRenderer = new RazorRenderer();
//razor内容
var template = "你好@Model.Name先生, 请核实您的电话号码是否为@Model.Phone";
var email = Email
    .From("lisi@126.com")
    .To("zhangsan@qq.com")
    .Subject("手机号核实")
    //传递自定义POCO类
    //.UsingTemplate<UserInfo>(template, new UserInfo { Name = "张三", Phone吗 = "100110119120" })
    //或传递匿名对象
    .UsingTemplate(template, new { Name = "张三", Phone吗 = "100110119120" });
var result = await email.SendAsync();
```

当然它支持的方式不仅仅只是 Razor 字符串，还可以传递 Razor 视图文件

```C#
var email = Email
    .From("lisi@126.com")
    .To("zhangsan@qq.com")
    .Subject("手机号核实")
    //传递自定义POCO类
    //.UsingTemplateFromFile<UserInfo>($"{Directory.GetCurrentDirectory()}/template.cshtml",
    //     new UserInfo { Name = "张三", Phone吗 = "100110119120" });
    //第一个参数为视图文件位置，第二个参数为模型对象
    .UsingTemplateFromFile($"{Directory.GetCurrentDirectory()}/template.cshtml",
       new { Name = "张三", Phone吗 = "100110119120" });
var result = await email.SendAsync();
```

FluentEmail.Razor 之所以能够支持强大的 Razor 模板引擎，主要是得益于它内部集成了 RazorLight，这是一款非常强大的 Razor 引擎，可以将 Razor 模板字符串或者 Razor 视图文件解析成具体的字符串结果，具体详情可参阅 RazorLight 官方 GitHub 地址https://github.com/toddams/RazorLight，目前正式版并不支持.Net Core，可以选择下载 beta 版本

```shell
Install-Package RazorLight -Version 2.0.0-beta10
```

它的使用方式也非常简单

```C#
//razor字符串的方式
var engine = new RazorLightEngineBuilder()
	.UseEmbeddedResourcesProject(typeof(Program))
	.UseMemoryCachingProvider()
	.Build();
string template = "Hello, @Model.Name. Welcome to RazorLight repository";
ViewModel model = new ViewModel {Name = "John Doe"};
//result就是解析后的字符串
string result = await engine.CompileRenderStringAsync("templateKey", template, model);
```

或使用 razor 视图文件的方式

```C#
var engine = new RazorLightEngineBuilder()
	.UseFileSystemProject("${Directory.GetCurrentDirectory()}")
	.UseMemoryCachingProvider()
	.Build();
var model = new {Name = "John Doe"};
string result = await engine.CompileRenderAsync("template.cshtml", model);
```

当然它支持的方式不仅仅只有这两种，无论是使用便捷程度还是功能上都非常的强大，有兴趣的同学可以自行查阅 RazorLight 的 GitHub 地址，讲解的还是非常详细的。在这里就不在过多的讨论关于 RazorLight 的使用方式了。

关于发送的邮件内容,这里有一个非常重要的点需要友情提示一下`公共邮箱运营商比如网易或腾讯，有的可能需要手动开启SMTP服务`,具体如何设置可以参考https://blog.csdn.net/c13_tianming/article/details/47660635一文。还有一点也比较重要`如果你使用公共邮箱运营商的邮箱那么他们会对邮件的标题和内容限制比较大，可能出现的问题比较多，而且开启Smtp服务需要发送短信认证才能开启`。好在大部分公司都有自己的邮件系统，在实际发送邮件的过程中可能不会存在这么多的问题。

**结合依赖注入使用**

在使用.Net Core 的实际开发中，依赖注入已经成为了必不可少的开发模式。如果你正在使用.Net Core 开发项目，但是你还没有接触依赖注入，那么需要你先自行反省一下。FluentEmail 作为一款与时俱进的组件，也可以结合依赖注入使用，使用这种方式我们可以在注册的时候统一的配置一些默认的设置。这波操作就不需要额外引入一些别的包了，如果你需要使用 Smtp 就引入 FluentEmail.Smtp 包，如果你需要使用 Razor 模板就引入 FluentEmail.Razor 包，关于注入的这一部分的功能其实是包含在 FluentEmail.Core 包里面的

```C#
public void ConfigureServices(IServiceCollection services)
{
    SmtpClient smtp = new SmtpClient
    {
        //smtp服务器地址(我这里以126邮箱为例，可以依据具体你使用的邮箱设置)
        Host = "smtp.qq.com",
        UseDefaultCredentials = true,
        DeliveryMethod = SmtpDeliveryMethod.Network,
        //这里输入你在发送smtp服务器的用户名和密码
        Credentials = new NetworkCredential("zhangsan@qq.com", "zhangsan")
    };
    //注入的时候可以添加一些默认的设置
    services
        //设置默认发送用户
        .AddFluentEmail("zhangsan@qq.com")
        //添加razor模板支持
        //.AddRazorRenderer($"{Directory.GetCurrentDirectory()}/Views")
        .AddRazorRenderer()
        //配置默认的smtp服务信息
        .AddSmtpSender(smtp);
}
```

在需要发送邮件的类中直接注入 IFluentEmail,不必惊慌咱们上面使用的 Email 这个类其实就是实现了 IFluentEmail 这个接口，所以使用方式上是完全一致的

```C#
public async Task<IActionResult> SendEmail([FromServices]IFluentEmail email)
{
     var result = await email//发送人
        //发送人
        .From("zhangsan@126.com")
        //收件人
        .To("lisi@qq.com")
        //抄送人
        .CC("admin@126.com")
        //邮件标题
        .Subject("邮件标题")
        //邮件内容
       .Body("邮件内容").SendAsync();
    return View();
}
```

如果你需要发送 Razor 视图模板相关的内容，也还是那个熟悉的配方那个熟悉的味道，没有任何的不同，只是省略了一些我们在注册的时候添加的一些默认配置

```C#
public async Task<IActionResult> SendEmail([FromServices]IFluentEmail email)
{
     var template = "你好@Model.Name先生, 请核实您的电话号码是否为@Model.Phone";
     var result = await email//发送人
        .From("lisi@126.com")
        .To("zhangsan@qq.com")
        .Subject("手机号核实")
        //传递自定义POCO类
        //.UsingTemplate<UserInfo>(template, new UserInfo { Name = "张三", Phone吗 = "100110119120" })
        //或传递匿名对象
        .UsingTemplate(template, new { Name = "张三", Phone吗 = "100110119120" })
        .SendAsync();
    return View();
}
```

## 总结

关于 FluentEmail 的基本使用方式我们就介绍到这里，我个人感觉它自身的功能还是非常强大的，而且使用起来非常的简单。说实话在之前我没接触到 FluentEmail 之前，我经常在园子里看到其他语言集成发送邮件的组件，确实非常强大，比如在 springboot 中集成 spring-boot-starter-mail 真的是非常的便捷。后来无意中接触到了 FluentEmail 心里还是蛮欣慰的，一是它强大的功能和易用性，其次是可以去结合.Net Core 进一步优化了它的使用方式，至少在.Net 和.Net Core 中我们也拥有一款非常便捷的邮件发送组件。FluentEmail 的作者也呼吁更多的开发者能够了解并参与到 FluentEmail 开发和实践中去，最后再次贴上它的 GitHub 地址https://github.com/lukencode/FluentEmail，有兴趣的可以去了解学习一下顺便别忘了给个Star。
