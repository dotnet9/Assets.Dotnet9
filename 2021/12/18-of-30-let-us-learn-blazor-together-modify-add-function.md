---
title: (18/30)大家一起学Blazor：修改Add()方法
slug: 18-of-30-let-us-learn-blazor-together-modify-add-function
description: 假设今天有个状况是这样：有一条日志，新增第二条但还没提交前，想将第一条删除，这时会发生什么事呢？
date: 2021-12-20 23:04:06
copyright: Reprinted
author: StrayaWorker
originalTitle: (18/30)大家一起学Blazor：修改Add()方法
originalLink: https://ithelp.ithome.com.tw/articles/10267015
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

假设今天有个状况是这样：有一条日志，新增第二条但还没提交前，想将第一条删除，这时会发生什么事呢？

竟然出错了！明明只是将要删除的`Post.Id`提交后端去，为什么会有这样的错误信息？

![](https://img1.dotnet9.com/2021/12/2801.png)

这就要说到 C# 的特性了，C# 是面向对象(Object-Oriented Programming, OOP)语言，也就是说任何东西包括数据、方法都能变成对象，`Blog`、`Post`就是一个个对象，除了对象这种引用类型，也有单纯的`int`、`bool`等基础类型。

**(注：`string`分类上是引用类型，但语法上却是基础类型，这是为了避免无数的 string 撑满内存。)**

基础类型的意思是：两个基础类型之间的修改不会影响彼此。定义一个变量`int a = 0;`，再定义`int b = a;`，`b`等于 0 这没问题，这时候如果再赋值`b = 3;`，`a`跟`b`就不相等了，彼此间不会影响对方。下图用 LINQPad 示范，`Dump()`的意思是将该变量显示在下方`Results`区块，可以看到即便中间修改`b`的值，`a`也不受影响。

![](https://img1.dotnet9.com/2021/12/2802.png)

引用类型则是：B 对象如果来自 A 对象，不论哪个对象修改，另一个就会跟着修改。可以看到下图在 12 行将`B`对象的`Title`改为`"BB"`，结果`A`对象的`Title`也跟着变了。

![](https://img1.dotnet9.com/2021/12/2803.png)

那这些跟`Blog`有什么关系呢？我们看后端`BlogRepository.cs`的`GetBlog()`，可以看到这边将`blog`回传，前端`BlogBase.razor.cs`这边接起来后，一旦触发`Add()`就会在`Blog.Posts`新增一条`PostModel`。

![](https://img1.dotnet9.com/2021/12/2804.png)

前端点击`Delete`按钮后，后端`PostRepository.cs`的`DeletePost()`这边会触发`SaveChanges()`，这时候的`Blog.Posts`会有一条没有`Blog`、`Title`跟`Content`的`PostModel`，这条根本还没点击`Submit`按钮经由后端存到数据库，是只存在于前端的数据，但是触发`SaveChanges()`的时候却试图将这条数据存进数据库，`Title`跟`Content`是不能为`null`的，自然就出错了。

![](https://img1.dotnet9.com/2021/12/2805.png)

![](https://img1.dotnet9.com/2021/12/2806.png)

另外如果单纯将数据库的`Posts`取出来，是看不到那一条数据的，因为那是跟着`Blog`的`PostModel`。

![](https://img1.dotnet9.com/2021/12/2807.png)

要解决这问题有几种方法，第一种是将`Blog`跟`Post`完全拆开，两者各有自己的前端页面，不过如果现实情况的项目遇到这种坑(没错，这是笔者给自己挖的坑…)，往往不会有时间做这种重构。

第二种方法是当后端`PostRepository.cs`收到没有`Title`的`PostModel`时，回传提示信息。

![](https://img1.dotnet9.com/2021/12/2808.png)

前端`PostBase.razor.cs`修改为以`deleted.IsSuccess`判断，删除成功则将`Post!.Id`传给`Blog`将该条`Post`从页面删除，失败的话提示失败的原因。

![](https://img1.dotnet9.com/2021/12/2809.png)

![](https://img1.dotnet9.com/2021/12/2810.gif)

虽然以工程师的角度来看这样避免了错误，但以`UX (User Experience)` 角度来看根本就是莫名其妙，为什么删除一条日志还要限制不能有空的日志？所以就要用第三种方法。

第三种是建立`ViewModel`，页面的`CRUD`都针对`ViewModel` 处理，之后才一一`Mapping` 回去`Model`。

所谓的`ViewModel` 是指不存在于数据库但又希望呈现在页面上的字段，例如有张 table`Employee`里面有两个字段`FirstName`跟`LastName`，存进数据库时分开存，但显示时希望动些手脚(例如要组合起来且全大写)，可以把两个字段都丢到前端后再处理，由使用者的浏览器处理，也可以先在后端处理好再用`ViewModel` 承接丢到前端。

另一个例子是信用卡，table`CreditCard`存有使用者的信用卡号、三位数认证码、出生年月日，大家应该常常网购，刷卡时会让使用者看到信用卡末四码，这种机密隐私数据总不可能 16 码都丢到前端处理吧？这时就需要在后端处理后再由`ViewModel` 传到前端了。

我们先建立 `BlogViewModel` 跟`PostViewModel`，因为是`ViewModel` 所以不需要用跟数据库相关的`[Key]`attribute，有使用到`Model`的地方都改成`ViewModel`。

![](https://img1.dotnet9.com/2021/12/2811.png)

接着修改后端`BlogRepository.cs`，页面呈现改成`ViewModel`，数据存取沿用`Model`，可以看到 36 到 56 行手动做 Mapping。

![](https://img1.dotnet9.com/2021/12/2812.png)

![](https://img1.dotnet9.com/2021/12/2813.png)

`PostRepository.cs`的`CreatePost()`也是一样，`DeletePost()`则把原本的 else 区块对`Blog.Posts`的判断移除。

![](https://img1.dotnet9.com/2021/12/2814.png)

![](https://img1.dotnet9.com/2021/12/2815.png)

`BlogBase.razor.cs`跟`PostBase.razor.cs`把原本用到的`Model` 改成`ViewModel`。

![](https://img1.dotnet9.com/2021/12/2816.png)

这时候来建立新数据，不过建立第二条后紧接着要删除第二条，却发生找不到 Post 的问题，这是为什么？

![](https://img1.dotnet9.com/2021/12/2817.png)

原来第二条虽然进入数据库了，但我们没有重新将数据取回来，页面的 Blog.Posts 第二条的 Post.Id 仍然是 0。

![](https://img1.dotnet9.com/2021/12/2818.png)

为了让`Blog.Posts`知道要重取数据库，我们要在`PostBase.razor.cs`新增`EventCallback`，告知`BlogBase.razor.cs`再执行一次`LoadData()`，因为是告知而已，就不用传`<TValue>`。

![](https://img1.dotnet9.com/2021/12/2819.png)

![](https://img1.dotnet9.com/2021/12/2820.png)

![](https://img1.dotnet9.com/2021/12/2821.png)

然后在新增第二条之后立刻删除，就会正常了。新增第二条后再新增第三条，删除第二条也会正常。

(注：如果看到下图的错误信息，有可能是[Visual Studio 的问题](https://stackoverflow.com/a/65065727)，先试试重启 Visual Studio。)

![](https://img1.dotnet9.com/2021/12/2822.png)

**引用：**

1. [.NET Stack and Heap](https://www.youtube.com/watch?v=clOUdVDDzIM)
2. [In C#, why is String a reference type that behaves like a value type?](https://stackoverflow.com/questions/636932/in-c-why-is-string-a-reference-type-that-behaves-like-a-value-type)
3. [What is ViewModel in MVC?](https://stackoverflow.com/questions/11064316/what-is-viewmodel-in-mvc)
4. [Understanding ViewModel in ASP.NET MVC](https://www.dotnettricks.com/learn/mvc/understanding-viewmodel-in-aspnet-mvc)

**注：本文代码通过 .NET 6 + Visual Studio 2022 重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**
