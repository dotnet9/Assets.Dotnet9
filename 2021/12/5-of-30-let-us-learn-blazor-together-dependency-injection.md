---
title: (5/30)大家一起学Blazor：依赖注入(Dependency Injection)
slug: 5-of-30-let-us-learn-blazor-together-dependency-injection
description: 问题来了，为什么Blazor会知道WeatherForecastService在这里可以调用？
date: 2021-12-11 13:27:42
copyright: Reprinted
author: StrayaWorker
originalTitle: (5/30)大家一起学Blazor：依赖注入(Dependency Injection)
originalLink: https://ithelp.ithome.com.tw/articles/10260278
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
albums:
    - 一起学Blazor系列
categories: 
    - Blazor
tags: 
    - Blazor
    - ASP.NET Core
    - 学Blazor
---

问题来了，为什么 Blazor 会知道 WeatherForecastService 在这里可以调用？

点开 Program.cs，可以找到一行代码：

```C#
builder.Services.AddSingleton<WeatherForecastService>();
```

把这段代码注释，重新加载网页，点击`Fetch data`菜单，可以在页面看到下面的异常警告信息（只在页脚显示了一个警告块），详细警告看终端输出，因为我们试图在 FetchData.razor 调用 WeatherForecastService，却没告诉 Blazor 我们要注册这个服务。

![页面异常警告提示](https://img1.dotnet9.com/2021/12/1001.png)

![终端异常打印](https://img1.dotnet9.com/2021/12/1002.png)

复制提示看看，这个提示很明确：

```shell
Cannot provide a value for property 'ForecastService' on type 'BlazorServer.Pages.FetchData'. There is no registered service of type 'BlazorServer.Data.WeatherForecastService'.
```

不过这并不是 day03 说到的依赖注入，依赖注入的目的是摆脱高层级程序必须依赖于低层级程序的窘境，以减少耦合性。举例来说，如果今天 FetchData.razor 要调用其他 Service，例如 NewWeatherForecastService 的同名方法 GetForecastAsync，取回 10 条数据，那只要用到 WeatherForecastService 的地方都必须修改，目前因为 Demo 的关系不多所以没感觉，如果日后有 10 几 20 几个地方要改呢？

这时候就是依赖注入发挥功能的时候了，先定义一个接口：`interface IWeatherForecastService`

```C#
namespace BlazorServer.Data;

public interface IWeatherForecastService
{
	Task<WeatherForecast[]> GetForecastAsync(DateTime startDate);
}
```

里面就写我们要的方法：`Task<WeatherForecast[]> GetForecastAsync(DateTime startDate);`

也不用实现(虽然接口也能实现：`站长注：在C#8.0中，针对接口引入了一项新特性，就是可以指定默认实现，方便对已有实现进行扩展，也对面向Android和Swift的Api进行互操作提供了可能性。`)，接着让 WeatherForecastService 跟 NewWeatherForecastService 继承 IWeatherForecastService

![服务实现接口](https://img1.dotnet9.com/2021/12/1003.png)

Program.cs 改用 IWeatherForecastService 跟 NewWeatherForecastService 注册

![接口注入服务](https://img1.dotnet9.com/2021/12/1004.png)

看上面截图，在 FetchData.razor 中，也改为注入 IWeatherForecastService

重整加载网页就能看到数据条数变为 10 条了

![10条数据展示](https://img1.dotnet9.com/2021/12/1005.png)

依赖注入的核心就是「对某个功能的依赖性是通过注入的方式」，不直接调用底层程序，而是调用底层程序的接口，即便底层程序修改也不会导致所有调用该程序的调用端都必须改动。

**注：由于笔者是在写完这篇之后才想起来生命周期，原本想用 git rebase 的功能回到这一次的 commit 新增 Demo，但会有 git 断头疑虑，所以笔者会在 day 07 再说明生命周期，若有不便敬请见谅。**

**注：本文代码通过 .NET 6 + Visual Studio 2022 重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**
