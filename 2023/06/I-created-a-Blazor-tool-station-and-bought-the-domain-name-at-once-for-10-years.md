---
title: 搞了个Blazor工具站，域名一次性买了10年！
slug: I-created-a-Blazor-tool-station-and-bought-the-domain-name-at-once-for-10-years
description: 网站使用Blazor Wasm开发，网站内容包括在线工具和在线小游戏两个种类，主要是体验Web Assembly到底好不好。
date: 2023-06-24 22:44:29
lastmod: 2023-06-25 13:54:47
copyright: Default
draft: false
cover: https://img1.dotnet9.com/2023/06/cover_12.png
categories: Blazor
tags: Dotnetools,WASM,Blazor WebAssembly,Client
---

大家好，我是沙漠尽头的狼。

在 [Dotnet9](https://dotnet9.com) 上线在线小工具和小游戏后，服务器的压力感觉挺大的，打开25个页面，内存占用170MB左右，CPU保持在60~70%，看来Server真不适合搞这类交互较多的程序（服务器配置：2核4G内存），所以站长加急上线 [Blazor Wasm](https://dotnetools.com) 版本网站，便于大家直观对比了解两种模式的区别，下面请看我细说。

## 1. 关于上线Dotnet工具箱

为了后面工具和游戏的扩展，站长把去年买的域名 [dotnetools.com](https://dotnetools.com) 用上了，该域名一次性买了10年（不用担心网站过几年消失，当然不排除意外，比如站长没钱续费服务器。。。），并赶紧在1天之内开发并部署了一个 [Blazor Wasm](https://dotnetools.com) 版本网站，把在 [Dotnet9](https://dotnet9.com) 已经上线的在线小工具和游戏同步搬过来了，大家可以体验下：https://dotnetools.com：

![](https://img1.dotnet9.com/2023/06/cover_12.png)

## 2. Dotnet工具箱网站开源的

网站源码组织结构如下，只有一个工程，为了快速上线，代码也比较清晰明了：

![](https://img1.dotnet9.com/2023/06/1301.png)

网站源码链接在文末，别再问我源码地址了。。。。

## 3. Blazor Server为什么不适合开发在线工具或游戏之类的网站应用？

Blazor Server 不适合开发在线工具或游戏之类的网站应用，主要是因为它的工作原理和特性导致了一些限制和不适用的情况。

1. 实时性限制：Blazor Server 使用了 SignalR 技术来实现与服务器的实时通信，但由于所有的 UI 更新都需要通过服务器来完成，因此在网络延迟较高的情况下，用户可能会感受到明显的延迟。这对于在线工具或游戏等需要实时响应的应用来说是不可接受的。

2. 服务器资源消耗：Blazor Server 的工作原理是将整个应用部署在服务器上，每个用户都会占用一个连接和一些服务器资源。对于在线工具或游戏等需要大量用户同时在线的应用来说，这可能会导致服务器资源消耗过大，难以扩展和维护。

3. 客户端性能限制：Blazor Server 的 UI 渲染是在服务器上完成的，然后通过 SignalR 将更新的 UI 推送到客户端。这意味着客户端的性能对于应用的响应速度和用户体验有很大影响。对于一些复杂的在线工具或游戏来说，客户端的性能可能无法满足需求。

综上所述，Blazor Server 更适合开发那些对实时性要求不高、用户量较小、对服务器资源消耗要求不高的网站应用。对于在线工具或游戏等需要实时性和大量用户同时在线的应用，Blazor WebAssembly 可能更适合，因为它可以将整个应用部署到客户端，减轻了服务器的负担，并提供了更好的用户体验。

## 4. 选择Blazor Wasm开发工具站理由

[Dotnet9](https://dotnet9.com) 网站选择Blazor Server依然不变，因为博客类网站需要SEO，需要搜索引擎提供流量。

而 [Dotnet工具箱](https://dotnetools.com) 主要关注的是在线小工具和小游戏，所以选择Blazor Wasm，当谈到选择Blazor WebAssembly时，有几个令人兴奋的优势值得一提：

1. 即时性能：Blazor WebAssembly利用WebAssembly（Wasm）技术，将C#代码编译成高效的二进制格式，可以在浏览器中直接运行。这意味着您可以在客户端使用C#编写的应用程序，而无需将其转换为JavaScript。这种直接运行的能力使得Blazor WebAssembly具有接近原生应用程序的性能，为用户提供更快的加载速度和更流畅的用户体验。

2. 跨平台：Blazor WebAssembly是一个跨平台的解决方案，可以在各种操作系统和设备上运行，包括Windows、Mac、Linux和移动设备。这意味着您可以使用相同的代码库构建适用于不同平台的应用程序，从而减少开发和维护的工作量。

3. 开发效率：Blazor WebAssembly使用C#语言和.NET框架，这是一个广泛使用的开发工具和生态系统。如果您已经熟悉C#和.NET，那么您可以立即开始使用Blazor WebAssembly进行开发，无需学习新的语言或框架。这种开发效率可以大大加快项目的开发速度，并减少开发人员的学习曲线。

4. 强大的生态系统：Blazor WebAssembly是基于.NET生态系统构建的，这意味着您可以利用.NET的丰富功能和第三方库来构建功能强大的应用程序。您可以使用.NET的各种工具和技术，如Entity Framework、ASP.NET Core等，来简化开发过程并提高应用程序的质量和可维护性。

5. 安全性：Blazor WebAssembly应用程序在客户端运行，但它们是在沙箱环境中执行的，与原生应用程序相比，它们具有更高的安全性。这意味着您可以在客户端执行敏感操作，而无需担心安全问题。此外，由于使用C#编写代码，您可以利用.NET的安全功能来保护应用程序免受常见的安全漏洞和攻击。

综上所述，Blazor WebAssembly具有即时性能、跨平台、开发效率、强大的生态系统和安全性等令人兴奋的优势。这些优势使得Blazor WebAssembly成为一种令人激动的技术选择，为开发人员提供了构建高性能、跨平台的Web应用程序的新方式。

**重点**：WASM才是Blazor的未来。

## 5. 详细对比Blazor Server和Blazor Wasm

[Dotnet9](https://dotnet9.com) 网站选择Blazor Server，可在在线工具和在线游戏页面体验Server，比如 [扫雷游戏](https://dotnet9.com/games/minesweeper)，在游戏页面也可选择跳转到 [Dotnet工具箱](https://dotnetools.com) 的 [扫雷游戏](https://dotnetools.com/games/minesweeper) 页面，这是Wasm版本，可通过浏览器F12打开开发者工具查看网络请求情况，下面简单说说查看步骤。

[Dotnet9](https://dotnet9.com) 页面的 [扫雷游戏](https://dotnet9.com/games/minesweeper)，点击工具栏可以跳转到 [Dotnet工具箱](https://dotnetools.com) 的 [扫雷游戏](https://dotnetools.com/games/minesweeper) 页面：

![](https://img1.dotnet9.com/2023/06/1302.png)

[Dotnet9](https://dotnet9.com) 页面的 [扫雷游戏](https://dotnet9.com/games/minesweeper)页面，看网络请求，几乎一直在请求，简直令人发指，丧心病狂：

![](https://img1.dotnet9.com/2023/06/1304.png)

[Dotnet工具箱](https://dotnetools.com) 的 [扫雷游戏](https://dotnetools.com/games/minesweeper) 页面，看网络请求，只有网页的图片请求，其他请求就没有，这就是客户端的魅力（WebAssembly）：

![](https://img1.dotnet9.com/2023/06/1303.png)

关于Blazor，或Blazor Server与Blazor Wasm，相信很多人只是听说过，对相关的概念了解不多，这里通过询问GPT，回答的和前面段落类似，这里再对比列出给大家做个普及吧：

Blazor是一个用于构建Web应用程序的开源框架，它允许使用C#和.NET来编写客户端代码。Blazor有两种模式：Blazor Server和Blazor WebAssembly（Wasm）。下面是对这两种模式的详细对比：

1. 架构：

 - Blazor Server：Blazor Server模式将应用程序的UI渲染在服务器上，然后通过SignalR将UI更新推送到客户端。客户端通过与服务器建立持久连接来接收UI更新和处理用户交互。
 - Blazor WebAssembly：Blazor Wasm模式将整个应用程序的代码和依赖项编译为WebAssembly格式，并在客户端上运行。应用程序在客户端上独立运行，不需要与服务器保持持久连接。

2. 性能：

 - Blazor Server：Blazor Server模式的性能受到服务器和网络的影响。由于UI渲染在服务器上进行，因此对于大型应用程序或高并发场景，可能会对服务器造成较大的负载。
 - Blazor WebAssembly：Blazor Wasm模式的性能主要受到客户端设备的影响。由于应用程序在客户端上运行，因此可以获得更好的响应速度和更低的延迟。

3. 可靠性：

 - Blazor Server：Blazor Server模式依赖于服务器的稳定性和可用性。如果服务器出现故障或网络连接中断，应用程序将无法正常工作。
 - Blazor WebAssembly：Blazor Wasm模式在客户端上独立运行，不依赖于服务器的稳定性和可用性。即使服务器不可用，应用程序仍然可以继续运行。

4. 开发体验：

 - Blazor Server：Blazor Server模式的开发体验类似于传统的服务器端开发，可以使用.NET的完整功能和生态系统。但是，由于UI渲染在服务器上，因此在处理用户交互时需要考虑与服务器的通信延迟。
 - Blazor WebAssembly：Blazor Wasm模式的开发体验类似于客户端开发，可以使用C#和.NET的大部分功能，但某些功能可能受到限制。由于应用程序在客户端上运行，因此可以提供更接近原生应用程序的用户体验。

总的来说，Blazor Server适用于需要快速开发和部署的应用程序，而Blazor WebAssembly适用于需要更好的性能和独立运行的应用程序。选择哪种模式取决于应用程序的需求和优先级。

## 6. 最后的话

前面两篇文章，有部分网友建议站长使用Wasm模式，站长已经成功部署上线了，大家有什么工具需求欢迎留言，站长有空会考虑加上，当然希望大家能PR工具和游戏，只限于Blazor WASM开发的哦。

今天分享到这，十分感谢您的阅读。

- 网站地址：https://dotnetools.com/
- 网站源码：https://github.com/dotnet9/dotnetools
- .NET版本： [.NET 8.0.0-preview.5.23280.8](https://dotnet.microsoft.com/zh-cn/download/dotnet/8.0)
- 微信技术群：添加站长微信（dotnet9com），一定要备注【入群】2个字
- QQ技术群：771992300