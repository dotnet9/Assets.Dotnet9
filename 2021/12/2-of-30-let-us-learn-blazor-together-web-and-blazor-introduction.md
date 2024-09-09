---
title: (2/30)大家一起学Blazor：网页和Blazor介绍
slug: 2-of-30-let-us-learn-blazor-together-web-and-blazor-introduction
description: 笔者对网站的认知为前端、后端及数据库，使用者在浏览器页面按下按钮或是表单请求，触发前端事件，将收集起来的条件打包送往后端
date: 2021-12-09 23:04:23
copyright: Reprinted
author: StrayaWorker
originalTitle: (2/30)大家一起学Blazor：网页和Blazor介绍
originalLink: https://ithelp.ithome.com.tw/articles/10258950
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
categories: 
    - .NET
tags: 
    - Blazor
    - ASP.NET Core
    - 学Blazor
---

笔者对网站的认知为前端、后端及数据库，使用者在浏览器页面按下按钮或是表单请求，触发前端事件，将收集起来的条件打包送往后端，后端接收条件后去数据库据此处理判断，捞出使用者想要的数据后，后端将页面、数据回传给前端，前端再将相应数据呈现在页面上，这就是最原始的前后端交流。

![](https://img1.dotnet9.com/2021/12/0601.jpg)

后来有人发现每次都要刷新页面实在太麻烦，而发展出了可以异步执行的 Ajax 技术，假如一个事件 A 没做完的话，其他事件 B, C 不会等 A 做完，而是会自己往下做，如此一来当使用者发送表单请求时，网页不会一直跑小圈圈等待刷新，而是会先让使用者看到页面，其他事在使用者看不到的地方继续处理，这样大大提升了使用者体验。

由于动态网页规范已经被 JS 统一，即便后来出现强类型的 TyepScript(也就是 Angular, React，Vue 等前端框架使用的语言)，最终仍要编译成浏览器认识的 JS，且 TyepScript 也是基于 JS，所以一个后端工程师若要自己写个网站，就不可避免地必须跟 JS 打交道，对于习惯强类型的人来说无异从头拓荒，若是遇到公司规范不严又遇到喜用任意类型的同事，一个变量可以一下使用 number，一下又使用 string，就足以让人抓狂了，幸好这时 Blazor 出现了。

Blazor 是 Browser 和 Razor 的合成字，代表在浏览器上执行的 Razor 组件。

Blazor 分为两种模式，WebAssembly Hosting 及 Server Hosting，简单来说就是 Client side 及 Server side，两者各有优缺点。不过在继续说下去前要先说明 WebAssembly 是什么。

WebAssembly 简称 Wasm，是一种二进制表示语言，任何程序语言经过特定编译都可以转成 Wasm，Wasm 的优点是将整个程序传到浏览器而不需要服务器，由于是二进制且已经编译过的关系，渲染网页的速度会比 JS 更快，文件也会更小。

Blazor WebAssembly 是将编译过的 dll 文件及.NET 运行时打包后发送到使用者的浏览器，所以第一次建立连接时会比较慢；Blazor Server 则是在服务器跟浏览器之间建立 SingalR 连接，当浏览器触发事件后，Server 处理完不是整页刷新(将所有 Html 元素送往前端)，而是通过 SingalR 将变化的元素(如 div)送往浏览器，这是因为 Blazor 也是如 Angular 使用 SPA(Single Page Application)模式，从头到尾只有一个页面，上面布满了不同功能的 Components，触发事件只会更新相关 Component。

## Blazor WebAssembly

优点：

1. 因为文件都在浏览器上，速度相较于 Blazor Server 更快
2. 不需要服务器
3. 不需要随时跟服务器连接
4. Client 端的浏览器被充分利用，减轻服务器负担
5. 可以架在任何服务器上，例如云端、微软的 Azure 甚至 CDN(Content Delivery Network，一种将数据暂存到离使用者地理位置更近的模式，比如说我如果想登入主机在美国的网站，速度一定比主机在台湾的网站慢得多，CDN 会将数据暂存在离台湾比较近的地区的主机，如香港、新加坡，让使用者连接速度更快)

缺点：

1. 第一次载入会花比较多时间，因为浏览器要下载.NET runtime 等组件(注：铁人赛前笔者建立了新的 Blazor WebAssembly 项目，发现已经没下载组件了，微软官方图片也没看到有下载组件，或许是新版本有所改动)
2. 受限于浏览器的处理能力
3. Client 端的软硬件都很重要

## Blazor Server

优点：

1. 载入速度比较快
2. 可以充分利用服务器的能力
3. 任一 Client 使用这软件唯一需要的只有浏览器
4. 由于源代码不会传到 Client 端所以会更安全

缺点：

1. 需要服务器
2. 需要跟服务器保持连接
3. 由于数据来回传递，延迟感会更重
4. 不容易提升运算能力，因为一个服务器能承受的 Client 端有限，微软给出的数据为一个单核配有 3.5G 内存的 Blazor Server 可以处理 5000 个连接；一个四核配有 14G 内存的 Blazor Server 可以处理 20000 个连接。

若将 Blazor WebAssembly 和 Blazor Server 的优缺点分别列出，可以看到没有一种模式是最完美的，只有最适合的。如果已经有了其他程序语言架构的服务器如 PHP, Node 或是 Rails，需要一个提供给使用者且不需要时刻连接服务器的 Client 端程序，Blazor WebAssembly 就是很好的选择，且 Blazor WebAssembly 具有 PWA(Progressive Web App)功能，虽然以网站模式开发却能让使用者像下载软件一样下载到桌面或是手机，知名网站如 Twitter, Pinterest, Starbucks 都是知名例子，如果用电脑开启 Twitter 网站，就能在网址列最右方看到下载的按钮；而如果需要从无到有生出一个需要频繁连接服务器(如对数据新增、修改、删除)的网站，就适合用 Blazor Server。

不过 Blazor 毕竟是微软的新产品，笔者也只用过 ASP.NET Core 搭配 Blazor，Blazor WebAssembly 想跟 PHP 等非微软语言开发的后端整合或许会有其他要注意的地方，若有相关需求的人可能要多方考量。

讲了一大堆文字，想必还是很多人跟笔者一开始接触时一样没有看懂，明天笔者会将两种项目都创建起来，让大家看一下两者差在哪里。

- 引用: [What is Blazor](https://www.youtube.com/watch?v=uuzi3SmCLVo)
- 引用: [ASP NET Core blazor hosting models](https://www.youtube.com/watch?v=xN1ffDrdYds)
- 引用: [The Differences Between Blazor WebAssembly And Blazor Server](https://hackernoon.com/the-differences-between-blazor-webassembly-and-blazor-server-wir31my)
- 引用: [了解 WebAssembly 的基础使用方法](https://blog.techbridge.cc/2017/06/17/webassembly-js-future/)
- 引用: [WebAssembly design](https://github.com/WebAssembly/design/blob/main/Security.md)
- 引用: [WHAT IS A CDN & WHERE DOES IT SHINE?](https://www.cdnetworks.com/what-is-a-cdn/)
- 引用: [Twitter](https://twitter.com/?lang=zh-tw)

**注：本文代码通过 .NET 6 + Visual Studio 2022 重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**

- 本文 Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/12/2021-12-09_03.md)
