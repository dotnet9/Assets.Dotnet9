---
title: (8/30)大家一起学Blazor：CSS样式修改和数据绑定详述
slug: 8-of-30-let-us-learn-blazor-together-css-style-modification-and-data-binding-details
description: 现在每次启动项目，预设路径都会是`/`，但我们目前没有Component套用这个路由，要自己切换到`Post`实在有些麻烦，另外Menu的图案也跟名称不符，我们来调整一下。
date: 2021-12-13 22:36:21
copyright: Reprinted
author: StrayaWorker
originalTitle: (8/30)大家一起学Blazor：CSS样式修改和数据绑定详述
originalLink: https://ithelp.ithome.com.tw/articles/10261579
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

现在每次启动项目，预设路径都会是`/`，但我们目前没有 Component 套用这个路由，要自己切换到`Post`实在有些麻烦，另外 Menu 的图案也跟名称不符，我们来调整一下。

我们先来改 icon，由于`font-awesome`目前已用会员制，必须登录才能产生一套免费的 icon 集合，因此笔者使用`bootstrap`的`icon`。首先去`bootstrap`的[`icon`](https://icons.getbootstrap.com/)页面下载`zip`文件(不想下载文件的人可以直接引用 CDN)，将文件解压存放在`wwwroot`，在`_Layout.cshtml`引用`bootstrap-icons.css`，在官网搜寻自己喜欢的 icon 套用即可，笔者这边还做了些样式调整。

![bootstrap下载icon页面1](https://img1.dotnet9.com/2021/12/1401.png)

![bootstrap下载icon页面2](https://img1.dotnet9.com/2021/12/1402.png)

![bootstrap下载icon页面2](https://img1.dotnet9.com/2021/12/1403.png)

![bootstrap下载icon页面3](https://img1.dotnet9.com/2021/12/1404.png)

![修改bootstrap类](https://img1.dotnet9.com/2021/12/1405.png)

![bootstrap图标](https://img1.dotnet9.com/2021/12/1406.png)

接着打开 Blazor Server 项目的`launchSettings.json`文件，在 profiles 内的 BlazorServer 输入这行"launchUrl": "Post"。

![](https://img1.dotnet9.com/2021/12/1407.png)

Day06 有说到绑定，不过只有稍微带过，因为当时的目的只是展示`form`，现在来细说一下。

Blazor 的数据绑定有分为单向绑定(one way binding)跟双向绑定(two way binding)，单向绑定就是在页面上输入`@variable`，有什么数据就显示什么。

![单向绑定](https://img1.dotnet9.com/2021/12/1408.png)

双向绑定则要用`@bind-value`将 input 内的数据跟页面绑在一起，页面输入的内容也会反向影响数据。

![双向绑定](https://img1.dotnet9.com/2021/12/1409.png)

如果有学过 Angular 的人应该会很熟悉，就是[ngModel]跟[(ngModel)]的用途，只是名字换了一个。

那 Blazor 有像 Angular 的(click)事件绑定吗？也是有的，不过若用`<InputText>`Component 会说到较为复杂如`EventCallBack`的内容，所以笔者这边先用单纯的`<input>`元素，之后讲到`EventCallBack`再回来说明。

先把`<InputText>`换成`<input>`，接着将`@bind-Value`改成`@bind`，再加入`@bind:event`，值为`html`的事件名，如`onchange`、`oninput`等等，这些事件在 MDN 都可以查到。接着在网页的输入框输入内容，就可以看到底下的字即时变换了，可以看到我的焦点虽然仍在 input 元素上，底下的内容已经改变了。

![事件绑定](https://img1.dotnet9.com/2021/12/1410.gif)

不过`oninput`跟`onchange`的使用时机最好再拿捏一下，如果使用`oninput`绑定`number`类型的数据，当使用者输入 1.5 的瞬间，就会被改为 1，这会让使用者困惑，若用`onchange`，则是在使用者移开焦点后才会将 1.5 改为 1。若非得用`oninput`的话，可以将绑定数据改为`nullable`或是字符串，再使用 getter,setter 自己做逻辑处理不合法数据。

那 Blazor 有类似 Angular 的`pipe`去改变网页的数据格式如 number、datetime 吗？目前有提供`@bind:format`绑定可以改变日期格式，我们先在`PostModel`加入一个`CreateDateTime`，接着复制一组标题的`div`贴上，将`label`跟`@bind`的绑定数据改一下，再把`@bind:event`改成`@bind:format`，就可以看到显示成指定的日期格式。

![格式绑定](https://img1.dotnet9.com/2021/12/1411.png)

要注意的是 Blazor 并没有对应`<select>`的 Component，因为 HTML 的`attribute`不能有`null`，最接近`null`的概念是移除`value`这个`attribute`，但如果选到一个没有`value`的`<option>`，浏览器会将该`<option>`的文字当成`value`。

**本文引用：**

- [ASP.NET Core Blazor data bindind](https://docs.microsoft.com/en-us/aspnet/core/blazor/components/data-binding?view=aspnetcore-5.0)
- [GlobalEventHandlers.onchange](https://developer.mozilla.org/en-US/docs/Web/API/GlobalEventHandlers/onchange)
- [Unparsable values](https://docs.microsoft.com/en-us/aspnet/core/blazor/components/data-binding?view=aspnetcore-5.0#unparsable-values-1)

**注：本文代码通过 .NET 6 + Visual Studio 2022 重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**
