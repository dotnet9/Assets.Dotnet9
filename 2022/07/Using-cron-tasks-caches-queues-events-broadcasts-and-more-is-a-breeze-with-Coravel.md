---
title: Coravel是.NetCore中开源的工具库，可以让你使用定时任务，缓存，队列，事件，广播等高级应用程序变得轻而易举！
slug: Using-cron-tasks-caches-queues-events-broadcasts-and-more-is-a-breeze-with-Coravel
description: Coravel 帮助开发人员在不影响代码质量的情况下快速启动和运行他们的 .NET Core 应用程序。
date: 2022-07-07 07:34:12
copyright: Reprinted
author: 黑哥聊dotNet
originaltitle: Coravel是.NetCore中开源的工具库，可以让你使用定时任务，缓存，队列，事件，广播等高级应用程序变得轻而易举！
originallink: https://mp.weixin.qq.com/s/q6mNrmulYKnnhqLKxubz2A
draft: False
cover: https://img1.dotnet9.com/2022/07/cover_06.png
categories: .NET相关
---

## Coravel

Coravel是.NetCore中开源的工具库，可以让你使用定时任务，缓存，队列，事件，广播等高级应用程序变得轻而易举！

Coravel 帮助开发人员在不影响代码质量的情况下快速启动和运行他们的 .NET Core 应用程序。

它通过为您提供简单、富有表现力和直接的语法，使高级应用程序功能易于访问和使用。

Github地址: [https://github.com/jamesmh/coravel](https://github.com/jamesmh/coravel)

## 安装

```shell
dotnet add package coravel
```

## 例子

### Task Scheduling

**配置**

在 .NET Core 应用程序的Startup.cs文件中，在ConfigureServices()方法内，添加以下内容：

```csharp
services.AddScheduler()
```

**使用**

然后在Configure()方法中，可以使用调度器：

```csharp
var provider = app.ApplicationServices;
provider.UseScheduler(scheduler =>
{
    scheduler.Schedule(
        () => Console.WriteLine("Every minute during the week.")
    )
    .EveryMinute()
    .Weekday();
});
```

### Queuing

**配置**

在您的Startup文件中，在ConfigureServices()：

```csharp
services.AddQueue();
```

**使用**

将接口的一个实例`Coravel.Queuing.Interfaces.IQueue`注入到控制器

```csharp
IQueue _queue;

public HomeController(IQueue queue) {
    this._queue = queue;
}
```

**同步**

```csharp
public IActionResult QueueTask() {
    this._queue.QueueTask(() => Console.WriteLine("This was queued!"));
    return Ok();
}
```

**异步**

```csharp
 this._queue.QueueAsyncTask(async() => {
    await Task.Delay(1000);
    Console.WriteLine("This was queued!");
 });
```

### Caching

**配置**

在Startup.ConfigureServices()：

```csharp
services.AddCache();
```

这将启用内存 (RAM) 缓存。

**使用**

要使用缓存，将`Coravel.Cache.Interfaces.ICache`通过依赖注入进行注入。

```csharp
private ICache _cache;

public CacheController(ICache cache)
{
    this._cache = cache;
}
```

### Event Broadcasting（事件广播）

Coravel 的事件广播允许侦听器订阅应用程序中发生的事件。

**配置**

在ConfigureServices方法中：

```csharp
services.AddEvents();
```

接下来，在Configure方法中：

```csharp
var provider = app.ApplicationServices;
IEventRegistration registration = provider.ConfigureEvents();
```

注册事件及其监听器

```csharp
registration
 .Register<BlogPostCreated>()
 .Subscribe<TweetNewPost>()
   .Subscribe<NotifyEmailSubscribersOfNewPost>();
```

**使用**

创建一个实现接口的类Coravel.Events.Interfaces.IEvent。就是这样！

事件只是将提供给每个侦听器的数据对象。它应该公开与此特定事件关联的数据。

例如，一个BlogPostCreated事件应该接受BlogPost创建的，然后通过公共属性公开它。

```csharp
public class BlogPostCreated : IEvent
{
    public BlogPost Post { get; set; }

    public BlogPostCreated(BlogPost post)
    {
        this.Post = post;
    }
}
```

创建一个新类，该类实现您将要监听的事件`Coravel.Events.Interfaces.IListener`的接口。**提示:每个侦听器只能与一个事件相关联。**

该IListener接口需要您实现`HandleAsync(TEvent broadcasted)`。

创建一个名为TweetNewPost的侦听器：

```csharp
public class TweetNewPost : IListener<BlogPostCreated>
{
    private TweetingService _tweeter;

    public TweetNewPost(TweetingService tweeter){
        this._tweeter = tweeter;
    }

    public async Task HandleAsync(BlogPostCreated broadcasted)
    {
        var post = broadcasted.Post;
        await this._tweeter.TweetNewPost(post);
    }
}
```

### Mailing

**配置**

安装 Nuget 包`Coravel.Mailer`，搭建一些基本文件：

- ~/Views/Mail/_ViewStart.cshtml- 配置邮件视图以使用 Coravel 的电子邮件模板
- ~/Views/Mail/_ViewImports.cshtml- 允许您使用 Coravel 的视图组件 
- ~/Views/Mail/Example.cshtml- 示例邮件视图
- ~/Mailables/Example.cs- 可邮寄样本

在Startup.ConfigureServices()：

```csharp
services.AddMailer(this.Configuration); 
```

**使用**

Coravel 使用`Mailables`发送邮件。`Mailables` 继承`Coravel.Mailer.Mail.Mailable`并接受一个泛型类型，该类型表示您希望与发送邮件相关联的模型。

```csharp
using Coravel.Mailer.Mail;
using App.Models;

namespace App.Mailables
{
  public class NewUserViewMailable : Mailable<UserModel>
  {
      private UserModel _user;

      public NewUserViewMailable(UserModel user) => this._user = user;

      public override void Build()
      {
          this.To(this._user)
              .From("from@test.com")
              .View("~/Views/Mail/NewUser.cshtml", this._user);
      }
  }
}
```

Mailable 的所有配置都在该Build()方法中完成。然后，您可以调用各种方法，例如To和From来配置收件人、发件人等。

如果大家对.net开源项目感兴趣可以持续关注我。