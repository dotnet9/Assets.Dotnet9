---
title: (14/30)大家一起学Blazor：JavaScript interop(互操作)
slug: 14-of-30-let-us-learn-blazor-together-javascript-interop
description: 虽然Blazor 不需要用到JavaScript，但某些已有的js库 还是很方便，不能因为不想用JavaScript 就全部舍弃，Blazor 就提供了调用JavaScript 的方法，这种情况称为JavaScript interoperability(简称JavaScript interop)。这篇就来实现Delete 按钮的提醒窗口，因为删除是很重要的功能，不能让使用者轻轻一按就轻易删除。
date: 2021-12-16 21:02:26
copyright: Reprinted
author: StrayaWorker
originalTitle: (14/30)大家一起学Blazor：JavaScript interop(互操作)
originalLink: https://ithelp.ithome.com.tw/articles/10263798
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
categories: .NET
tags: Blazor Server,学Blazor
---

虽然 Blazor 不需要用到 JavaScript，但某些已有的 js 库 还是很方便，不能因为不想用 JavaScript 就全部舍弃，Blazor 就提供了调用 JavaScript 的方法，这种情况称为 JavaScript interoperability(简称 JavaScript interop)。这篇就来实现 Delete 按钮的提醒窗口，因为删除是很重要的功能，不能让使用者轻轻一按就轻易删除。

Day 07 有说过，Blazor 有内置的 JavaScript Service，所以我们不用去 Program.cs 依赖注入，在想使用的页面注入即可。如果想在 razor 组件注入，只要输入`@inject IJSRuntime js`即可。

![](https://img1.dotnet9.com/2021/12/2201.png)

接着将原本的`ReturnPostId()`改名为`DeletePost()`，类型改为`Task`，里面的逻辑稍作修改，可以看到`JavaScript` 也是用`EventCallback` 的方式，除了会回传值的`InvokeAsync`，还有不回传值的`InvokeVoidAsync`可以用。第一个参数放的是`JS方法` 的名字，如果该`JS方法`有要求参数，就依序放在后面即可。

![](https://img1.dotnet9.com/2021/12/2202.png)

那如果想要自己写 C# 类把这段程序封装起来，好享受强类型的优势呢？Blazor 也提供了做法。首先在`Shared`文件夹新增`JsInteropClasses.cs`，里面只有做三件事，依赖注入、原本的 confirm 方法以及`Dispose`。

![](https://img1.dotnet9.com/2021/12/2203.png)

接着在`PostBase.razor.cs`一开始将 JS 注入进`JsInteropClasses`且继承`IDisposable`。

![](https://img1.dotnet9.com/2021/12/2204.png)

然后将原本的方法改成刚刚封装好的`_jsClass.Confirm()`方法，最后再重写`Dispose`方法。

![](https://img1.dotnet9.com/2021/12/2205.png)

可以看到跟原本直接在`PostBase.razor.cs`调用 JS 的`confirm`是一样的结果，差点忘了在`Post.razor`里修改 Delete 按钮的点击事件回调方法为`DeletePost`。

![](https://img1.dotnet9.com/2021/12/2206.png)

![](https://img1.dotnet9.com/2021/12/2207.gif)

在 Blazor 有 4 个地方可以载入 JS 文件，分别为：`<head>`、`<body>`、外部`JS script` 以及在 Blazor 启动后载入，都是将需要的`<script>`置于`_Layout.cshtml`(Blazor Server)或是`index.html`(Blazor WebAssembly)，比较特别的是最后一种，所以这边说明一下。

我们在`_Layout.cshtml`原本的`_framework/blazor.server.js`底下加入自己的 script，里面做的事情很简单只有`console.log`文字，要特别注意的是原本的 script 要加入一个 attribute `autostart="false"`，这是告诉 Blazor 不要自动启动程序，如果不加这个 attribute，就会得到这样的错误讯息。

![](https://img1.dotnet9.com/2021/12/2208.png)

![](https://img1.dotnet9.com/2021/12/2209.png)

不过目前的删除窗口太难看了，我们来换个套件，笔者看许多人使用了`SweetAlert2`，所以也来试试看。

首先去[SweetAlert2](https://sweetalert.js.org) 官网下载文件，接着将`sweetalert.js`放在`<head>`，然后把原本的第 4 种`Blazor.start()`移除，记得`_framework/blazor.server.js`的`autostart="false"`也要移除；或是直接写在 Blazor.start()里面也可以。

![](https://img1.dotnet9.com/2021/12/2210.png)

![](https://img1.dotnet9.com/2021/12/2211.png)

![](https://img1.dotnet9.com/2021/12/2212.png)

再加入自己的`<script>`，这边比较特别的是用了`Promise`，Promise 是用来让异步更好用的语法，`resolve`的意思是执行成功后会回传的内容(这边执行成功的意思是没有异常，也就是不论按下确定还是取消都会回传)。

![](https://img1.dotnet9.com/2021/12/2213.png)

`Confirm` 方法则改成调用刚定义好的`SweetConfirm()`。

![](https://img1.dotnet9.com/2021/12/2214.png)

点击 Delete 按钮，按下确定，`willDelete`为 true，于是`resolve`就将`willDelete` 传回去，`JsInteropClasses` 的`Confirm` 成功收到了`true`(这里有点小问题，点击确定后，先弹出了删除成功，`JsInteropClasses`才收到的`true`，后面有机会站长再优化)。

![](https://img1.dotnet9.com/2021/12/2215.png)

如果点击取消，`willDelete` 是 null。

![](https://img1.dotnet9.com/2021/12/2216.png)

Promise 是前端避不掉的语法，有兴趣的人可以看笔者附上的链接，笔者原本打算用 Observable 跟 subscribe 语法，不过 SweetAlert2 尚未实现。

另外 SweetAlert2 目前也有[Blazor 版本](https://github.com/Basaingeal/Razor.SweetAlert2)可以使用，方法大同小异，如果真的不想碰 JS 的人可以试试看。

最后来个本文效果动图，结束本文：

![](https://img1.dotnet9.com/2021/12/2217.gif)

**引用：**

1. [Blazor JavaScript interoperability (JS interop)](https://docs.microsoft.com/en-us/aspnet/core/blazor/javascript-interoperability/?view=aspnetcore-5.0)
2. [Call JavaScript functions from .NET methods in ASP.NET Core Blazor](https://docs.microsoft.com/en-us/aspnet/core/blazor/javascript-interoperability/call-javascript-from-dotnet?view=aspnetcore-5.0)
3. [HANDLING BUTTONS (CONFIRM, DENY, CANCEL)](https://sweetalert2.github.io/#handling-buttons)
4. [Day 20：Javascript interop](https://ithelp.ithome.com.tw/articles/10249044)
5. [JavaScript Promise 全介绍](https://wcc723.github.io/development/2020/02/16/all-new-promise/)
6. [Support ObservableLike #1982](https://github.com/sweetalert2/sweetalert2/issues/1982)
7. [Blazor - Making a Promise in JS - SweetAlert2 Confirmation Dialog](https://www.youtube.com/watch?v=P1nMiVpptGk)
8. [SweetAlert - upgrading-from-1x](https://sweetalert.js.org/guides/#upgrading-from-1x)

**注：本文代码通过 .NET 6 + Visual Studio 2022 重构，SweetAlert 使用的 2.0 版本，和原文出入较大，可点击原文链接与重构后代码比较学习，谢谢阅读。**
