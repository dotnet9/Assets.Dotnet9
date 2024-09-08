---
title: (29/30)大家一起学Blazor：Blazor单元测试
slug: 29-of-30-let-us-learn-blazor-together-unit-test-of-blazor
description: 开发一个系统最无聊的过程大概就是解决BUG了，尤其是那种尝试对null 对象取值的错误(`Object reference not set to an instance of an object.`)，这应该是大部分人刚踏入编程领域最常碰到的问题，为了从枯燥的解决BUG过程解脱，这篇就来介绍`单元测试`。
date: 2021-12-25 20:39:11
copyright: Reprinted
author: StrayaWorker
originalTitle: (29/30)大家一起学Blazor：Blazor单元测试
originalLink: https://ithelp.ithome.com.tw/articles/10274629
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
categories: .NET
tags: Blazor Server,学Blazor
---

开发一个系统最无聊的过程大概就是解决 BUG 了，尤其是那种尝试对 null 对象取值的错误(`Object reference not set to an instance of an object.`)，这应该是大部分人刚踏入编程领域最常碰到的问题，为了从枯燥的解决 BUG 过程解脱，这篇就来介绍`单元测试`。

`Blazor 的单元测试`跟一般 C# 编程不太一样，主要是`检查Component 的页面呈现逻辑`、`预期产生的HTML 标签跟实际页面有无差异`，毕竟 Blazor 是以后端语言编写而渲染的前端框架，如果要验证数据有无错误，就是一般 C# 的单元测试了。

目前微软的测试框架有`MSTest`、`NUnit` 跟`xUnit` 三种，`但都不包含Blazor`，还好有社区爱好者创建了`bUnit` 项目方便测试，不过`bUnit` 并非框架而是项目，所以必须先创建三种测试框架之一的项目再去`NuGet` 下载`bUnit`。

首先在方案底下创建测试项目`BlazorServerMsTest`，这边笔者用`MSTest` 框架，创建好后在项目名称点两下打开`csproj`文件，会看到 Sdk 为`"Microsoft.NET.Sdk"`，要改成`"Microsoft.NET.Sdk.Razor"`，否则`Blazor 编译器`不会渲染`Razor Component`，`TargetFramework`则要改成`net6.0`，这样才能跟我们的`BlazorServer` 项目相容。

![](https://img1.dotnet9.com/2021/12/4101.png)

![](https://img1.dotnet9.com/2021/12/4102.png)

![](https://img1.dotnet9.com/2021/12/4103.png)

接着去`NuGet` 下载`bunit`，并且引用主项目`BlazorServer`，如果要测试的 Component 没有用到数据，这样就完成前期作业了，但现实情况都是需要跟数据交互，也就是需要`Service`，所以要下载可以`产生假数据`的`Service`，笔者用的是`NSubstitute`。

![](https://img1.dotnet9.com/2021/12/4104.png)

(**注：**如果想把测试方法都写在 razor 文件的`@code 区块`，就需要在 `_Import.razor`放置用到的`namespace`，但因为笔者都用`code behind` 的方式就不放了。)

单元测试中分为三个部分，`Arrange`、`Act`、`Assert`，`Arrange` 是指`测试前的准备`，`Act` 是`找出要测试的项目`，`Assert` 则是`测试的结果`。

我们来测试第一个 `div.card` 的 HTML 结果，先打开网页去复制第一个有`card class`，这边的`User Id` 会随数据改变。

![](https://img1.dotnet9.com/2021/12/4105.png)

下图的 19 行创建了`bUnit` 这个测试实体并赋值给 ctx，20 行则`利用NSubstitute` 创建的假的`IUserRepository`，21 到 23 行调用了 `GetUsersAsync()` 不过丢了一个`假的List()`，里面只有一组`CustomUserViewModel`，这边故意给错误的数据，24 行`利用DI 注册 IUserRepository 服务`。

![](https://img1.dotnet9.com/2021/12/4106.png)

27 行`利用bUnit 渲染出 UserManagement` 并赋值给 cut (component under test)，但如果要比较整个 UserManagement 要贴上很多 HTML 标签，所以再`用 Find() 找出第一个有card` class 的标签，Find()用的是 CSS 选择器，代表可以放入标签、class 或是 id 等等。

31 行之后就是用找出来的 element 比较我们放进 `MarkupMatches()` 的 HTML 标签，笔者这边用的是多行，如果不想占据版面的人也可以全部浓缩成一行，`bUnit 不会比较断行`。

接着就是要实际测试了，可以按 `ctrl + r, a` 或是从上方的测试页签找到`Test Explorer`，然后就能看到测试失败，会告诉你`实际的HTML 跟预期的HTML 差在哪里`，因为我们给的假数据跟预期的不同，所以出错了。

![](https://img1.dotnet9.com/2021/12/4107.png)

![](https://img1.dotnet9.com/2021/12/4108.png)

我们把假数据的 UserId 跟 UsreName `改成跟预期的HTML 数据一样`，按下`ctrl r, t`，就能看到通过测试了。

![](https://img1.dotnet9.com/2021/12/4109.png)

![](https://img1.dotnet9.com/2021/12/4110.png)

**引用：**

1. [Creating a new bUnit test project](https://bunit.dev/docs/getting-started/create-test-project.html?tabs=mstest)
2. [Writing tests for Blazor components](https://bunit.dev/docs/getting-started/writing-tests.html?tabs=mstest)
3. [Mocking with NSubstitute](https://www.youtube.com/watch?v=aTx8_79QkDE)
