---
title: (4/30)大家一起学Blazor：Component及路由介绍
slug: 4-of-30-let-us-learn-blazor-together-component-and-routing-introduction
description: 由于笔者当初是用ASP.NET Core API + Blazor Server，所以会以Blazor Server示范，日后研究完Blazor WebAssembly会再将心得补上。
date: 2021-12-10 23:01:39
copyright: Reprinted
author: StrayaWorker
originalTitle: (4/30)大家一起学Blazor：Component及路由介绍
originalLink: https://ithelp.ithome.com.tw/articles/10260047
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

由于笔者当初是用 ASP.NET Core API + Blazor Server，所以会以 Blazor Server 示范，日后研究完 Blazor WebAssembly 会再将心得补上。

首先既然 Component 是可以重复利用的，我们在 Index.razor 放上两个 Counter，启动项目(如果不想完整调试，可以按 ctrl+F5，就会启动不调试模式，启动速度比较快，而且每次储存文件，Blazor 都会监测到，网页重新加载就可以载入新程序了)，浏览器上两个 Counter 有各自的 Click me 按钮，分别点击后可以看到数字分别增加，代表是不同的 Component，那这些数字又定义在哪里呢？

打开 Counter.razor，最上面是@page 指示词，这个稍后再说。再来是 html 跟一些 C#程序，最后是@code 区块，这就是 Blazor 的奇妙之处了，@code 相当于一般网页 JS 做的事情诸如定义变量、实现方法、发送 request 到后端或是 API，不过 Blazor 用 C#编写，这里定义了一个私有变量 currentCount，还有一个方法 IncrementCount()，调用这方法的是 Click me 按钮，每一次点击按钮都会使 currentCount+1，而呈现结果就在 p 元素内。

![Index.razor和Counter.razor](https://img1.dotnet9.com/2021/12/0801.png)

![两个Counter独立](https://img1.dotnet9.com/2021/12/0802.gif)

currentCount 定义的方式跟页面呈现就是一种模型绑定(model binding)，意思是数据跟页面有绑定关系，.NET Framework 的 View 的@model 或是@Viewbag，Angular 的[(ngModel)]也是同理，都是要做到数据流到页面后，对页面操作可以影响数据的行为。

我们来定义另一个变量 myClass，给这变量一些 bootstrap 的 class，再把变量放在 button 的 class 里面，记住在 html 里面用到 C#的程序必须以`@`开头，不然 Blazor 不知道要编译。重新加载页面可以看到按钮的样式变了，Blazor 帮我们把 myClass 的值 text-primary bg-warning 放进 button 的 class。

![添加myClass到Counter按钮](https://img1.dotnet9.com/2021/12/0803.png)

接着我们看 FetchData.razor，这里看到了@using BlazorServer.Data，我们待会可以把这个 using 放进\_import.razor，那么@inject WeatherForecastService ForecastService 又是什么呢？我们先看@code 区块，看到这里定义了 WeatherForecast 数组类型的变量 forecasts，且用异步方法 OnInitializedAsync 调用了 ForecastService.GetForecastAsync(DateTime.Now)，将结果回传 forecasts，眼尖的人应该发现了最上面的 ForecastService 跟@code 区块的 ForecastService 一模一样。

![FetchData.razor](https://img1.dotnet9.com/2021/12/0804.png)

我们点一下 GetForecastAsync()方法并按下 F12，可以看到这个方法回传的就是 5 个随机产生的天气数据阵列，html 里面有判断 forecasts 是否为 null，不是的话就产生一个 table，里面用 foreach 将 forecasts 的日期、摄氏、华氏及天气状态一一呈现出来。

![Service生成数据及渲染](https://img1.dotnet9.com/2021/12/0805.png)

前面说过 Blazor 只有一个网页，其他内容都是一个个 Component 组成的，每次触发事件，Server 或是 WebAssemlby 都会将相应 Component 呈现在浏览器上，但 Blazor 怎么知道现在要呈现哪个 Component 呢？

原因就是@page 指示词，这个指示词相当于传统的路由，可以看到 Index.razor 的@page 为"/"，表示这是首页，Counter.razor 及 FetchData.razor 也有相应的@page 指示词。一个页面可以有多个@page 指示词，不过开头`一定要有斜线且用双引号`包起来，笔者曾想过建立 enum 集中管理不同 Component 的@page，可惜目前 Blazor 不支持这种做法。另外若两个 Component 用了相同的@page，编译阶段就会出现错误提示，所以也不用担心若有重复路由 Blazor 会怎么处理。

![@page指示词](https://img1.dotnet9.com/2021/12/0806.png)

那么左边菜单的 Home, Counter, Fetch data 页面又是在哪里定义的呢？打开 MainLayout.razor，可以看到 NavMenu 元素，再打开 NavMenu.razor，可以看到三个 NavLink Component，这些 Component 会被 Server 翻译为浏览器认识的 a 元素，因此就算我们打开 Dev tool，也只会看到 a 元素。

![左侧菜单](https://img1.dotnet9.com/2021/12/0807.png)

![左侧菜单在html呈现为a标签1](https://img1.dotnet9.com/2021/12/0808.gif)

![左侧菜单在html呈现为a标签2](https://img1.dotnet9.com/2021/12/0809.png)

回到 MainLayout.razor，可以看到@Body 指示词，这就是其他 Component 会放置的地方，可以说是种 placeholder，再看 App.razor 里面有 Found 及 NotFound 两个 Component，从字面看就知道，前者是当输入的网址找到匹配的 Component 则会进入这里，后者则是找不到匹配的 Component，可以看到两者都用了 MainLayout。另外若有不同页面要套用不同 Layout，也可以自己定义。

![@body](https://img1.dotnet9.com/2021/12/0810.png)

![](https://img1.dotnet9.com/2021/12/0811.png)

说到这里，我们复习一下 Blazor Server 是怎么走的，可以看到跟 Angular 类似都是一层一层往下，管理较为方便。Blazor WebAssemlby 跟 Blazor Server 的 index.html 跟\_Layout.cshtml 大致相等，以及缺少了 appsettings.json 文件，通常会将程序跟数据库连接需要的连线字串放在这个文件，可证 Blazor WebAssemlby 确实只是被动接收数据，而无法主动跟数据库连接，笔者曾试过在这里引用 EF Core，也是无法让 Blazor WebAssemlby 接触数据库，在.NET Framework 的世界是用 XML 格式的 web.config，在.NET Core 则改用 JSON 格式的 appsettings.json。

引用: [ASP NET Core blazor project structure](https://www.youtube.com/watch?v=1MkPWOiwLIM)

**注：本文代码通过 .NET 6 + Visual Studio 2022 重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**
