---
title: Blazor开源组件库 - Masa Blazor
slug: blazor-is-spring-or-a-struggle-in-the-cold-wind
description: Blazor允许您`使用C#`而不是JavaScript`构建交互式`Web UI`。 Blazor应用由可重用的Web UI组件组成，这些组件使用C#、HTML和CSS实现。客户端和服务器代码都是用c#编写的，允许您共享代码和库。
date: 2021-12-16 12:57:34
copyright: Reprinted
author: MASA Blazor
originalTitle: Blazor是春天还是寒风里的挣扎
originalLink: https://www.cnblogs.com/doddgu/p/masa-blazor-0.html
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_19.png
albums:
    - Blazor组件库
categories: 
    - Blazor
tags: 
    - Blazor
---

## 官方解释 Blazor

Blazor 允许您`使用C#`而不是 JavaScript`构建交互式`Web UI`。 Blazor 应用由可重用的 Web UI 组件组成，这些组件使用 C#、HTML 和 CSS 实现。客户端和服务器代码都是用 c#编写的，允许您共享代码和库。

Blazor 是一个使用 [.NET](https://docs.microsoft.com/zh-cn/dotnet/standard/tour) 生成交互式客户端 Web UI 的框架：

1. 使用 [C#](https://docs.microsoft.com/zh-cn/dotnet/csharp/) 代替 [JavaScript](https://www.javascript.com/) 来创建信息丰富的交互式 UI。
2. 共享使用 .NET 编写的服务器端和客户端应用逻辑。
3. 将 UI 呈现为 HTML 和 CSS，以支持众多浏览器，其中包括移动浏览器。
4. 与新式托管平台（如 [Docker](https://docs.microsoft.com/zh-cn/dotnet/standard/microservices-architecture/container-docker-introduction/index)）集成。

使用 .NET 进行客户端 Web 开发可提供以下优势：

1. 使用 C# 代替 JavaScript 来编写代码。
2. 利用现有的 [.NET 库](https://docs.microsoft.com/zh-cn/dotnet/standard/class-libraries)生态系统。
3. 在服务器和客户端之间共享应用逻辑。
4. 受益于 .NET 的性能、可靠性和安全性。
5. 在 Windows、Linux 和 macOS 上使用 [Visual Studio](https://visualstudio.microsoft.com/) 保持高效工作。
6. 以一组稳定、功能丰富且易用的通用语言、框架和工具为基础来进行生成。

> 看到这里有些小伙伴手中的瓜已经要丢出来了，的确有部分是夸大了的，起码 VS 在三个平台高效工作这事儿，嗯。。。其他的继续吃瓜吧

## Blazor Vs MVC

### 什么是 MVC

官方解释：ASP.NET Core MVC 是使用“模型-视图-控制器”设计模式构建 Web 应用和 API 的丰富框架。

> 圈重点，Blazor 是交互式 Web UI，而 MVC 是 Web 应用和 API

### 什么是交互式 Web UI

谷歌、百度转了一圈，没有这个解释，连 Wiki 也是一脸懵逼。

尝试理解一下吧，交互式 Web UI 重点在于交互，而 Blazor 的官方解释是用 C#代替 JavaScript，那我们看看 JavaScript 有什么功能，我百度找一段过来：

1. 嵌入动态文本于 HTML 页面
2. 对浏览器事件做出响应
3. 读写 HTML 元素
4. 在数据被提交到服务器之前验证数据
5. 检测访客的浏览器信息。控制 cookies，包括创建和修改等

> 有这些基础功能，用户不需要在静态页面里跳来跳去了，的确体验会好很多

### Blazor 有什么优势

提供了一些交互能力，不再是纯粹的静态页，虽然 mvc 可以使用 JavaScript 达到同样的效果，但你需要掌握 JavaScript，甚至还要再学习 jQuery、Angular、Vue 等。而 Blazor 提供的交互能力则是使用 C#。

> 吹是吹完了，但你真的可以 100% C#吗？这很难，你会遇到各种问题，比如兼容性、性能等。好了，那我可以不用了吗？等等，下面还有瓜

## Blazor Vs 现代前端(Angular、Vue 等)

我们从几个方面来对比一下吧

### 调试

1. Blazor：Vistual Stuidio + F5，VS Code/命令行工具 + `dotnet watch`

> 比 WebPack 要快很多，跟 Vite 差不多
>
> 在非复杂场景下 Hot Reload 是可以的，但奇奇怪怪的问题太多了，前景很好，目前来看还是用 Ctrl + F5 启动或者用命令行吧
>
> VS 2022 的 Ctrl + F5 已经支持 Hot Reload 了

2. 现代前端：Webpack/Vite

### 全家桶

以 Vue 为例，Vue 全家桶包括 Vue Cli、Vue Router、Vuex

Blazor：

1. Cli：dotnet cli
2. Router：Microsoft.AspNetCore.Components.Routing.Router
3. Vuex：Blazor 状态管理，区别在于 WASM 状态保存在浏览器内存中，而 Server 保存在服务器内存中。而且 Blazor 状态管理更强大的是借助.Net 的能力，原生支持持久化存储、跨线路保存（Server 下共享服务器内存）、ASP.NET Core 受保护的浏览器存储（Server 独享功能）

### 组件库

主流的 Bootstrap, Ant Design, Material Design 等双方都有。但由于现代前端多年的积累，质量上的确有一定差距。

除了丰富程度上，Blazor 允许被 JavaScript 调用加载，并生成 Angualr、React 等组件。

> 虽然这看起来跟用 C#解决代替 JavaScript 有点冲突，但融入大环境也是不错的

下图演示的是 Blazor 提供的 inventory-grid Component 被 React 引用的例子(当然也可以给 Angular)：

> 更神奇的是，在 React 复用的 Blazor Component 居然也支持 Hot Reload。先不说 Hot Reload 到底如何，单是这个方向其实还是值得期待一下 Hot Reload 的未来吧。

![](https://img1.dotnet9.com/2021/12/1901.png)

不止可以给 React 提供复用的组件，还可以给 WPF

![](https://img1.dotnet9.com/2021/12/1902.png)

### 第三方库

举几个前端常用库来比较。

1. 网络：现代前端有 axios，Blazor 有 HttpClient
2. 数据操作：现代前端有 Lodash，Blazor 有 Linq
3. 时间：现代前端有 moment.js、Day.js，Blazor 有 DateTime 全家桶
4. 响应式编程：现代前端有 rx.js，Blazor 有 Rx.Net（没有用过，理论上.Net 基本都能用，欢迎纠错）
5. Mock：现代前端有 Mock.Js，Blazor 有 Moq，当然除了 mock 以外还有端到端的，双方也都有。

对比下来其实.Net 反而还有点优势，那就完美吗？当然不是，再说点劣势的部分吧。

1. Charts：现代前端有 ECharts 等，Blazor 不想说话

> 虽然目前 Blazor 的确没有成熟、免费的 Charts 组件库，但因为 Blazor 可以与 JS 交互的能力，调用 ECharts 也很简单，稍微考验一点点小伙伴的动手能力

2. 富文本编辑器、拖拽。。。
3. Blazor 骂骂咧咧的退出了群聊。。。

### 包管理

1. Blazor：NuGet
2. 现代前端：NPM、Yarn

### 性能

数据不直观，先从.Net Conf 2021 上的演示截图看一下：

![](https://img1.dotnet9.com/2021/12/1903.png)

有量化数据吗？有：

![](https://img1.dotnet9.com/2021/12/1904.png)

> 视频地址：[https://sec.ch9.ms/ch9/daba/468d5211-982b-4c86-8b51-e1c8824edaba/dotNETConfNewBlazorWebAssembly_high.mp4](https://sec.ch9.ms/ch9/daba/468d5211-982b-4c86-8b51-e1c8824edaba/dotNETConfNewBlazorWebAssembly_high.mp4)

那 AOT 可以解千愁吗？也不是。起码应用大小上来说的确也大了不少，但这并不妨碍 AOT 可以解决特定场景的问题。技术总要选择在适合的场景使用它，而不是盲目的。

![](https://img1.dotnet9.com/2021/12/1905.png)

### 完了吗

当然没有，其实这样比较对于 Blazor 是不公平的，因为 Blazor 站在.Net 的肩膀上有更多的亮点，比如原生支持的泛型、DI、面向对象设计（虽然 TS 也是）、数不过来的.Net 类库、跨平台应用（MAUI）等。

其实没有必要只看到 Blazor 的劣势，也可以看看站在.Net 上的前端能走多远，这不也是我们期待的吗？

看到这里，有些.Net 古董级大佬要出来发话了，Silverlight！是的，但这次 WASM 没有再要求下载插件了。

## Web Assembly Vs Server（服务器端渲染）

1. Web Assembly：WebAssembly 是一种新的编码方式，可以在现代的网络浏览器中运行 － 它是一种低级的类汇编语言，具有紧凑的二进制格式，可以接近原生的性能运行，并为诸如 C / C ++等语言提供一个编译目标，以便它们可以在 Web 上运行。它也被设计为可以与 JavaScript 共存，允许两者一起工作。

2. Server（Server Side Render - SSR）：将组件渲染为服务器端的 HTML 字符串，将它们直接发送到浏览器，最后将这些静态标记"激活"为客户端上完全可交互的应用程序。

### 为什么用 SSR

服务器端渲染 (SSR) 的优势主要在于：

1. 更好的 SEO，由于搜索引擎爬虫抓取工具可以直接查看完全渲染的页面。
2. 更快的内容到达时间 (time-to-content)

### 什么时候用 SSR

使用服务器端渲染 (SSR) 时还需要有一些权衡之处：

1. 开发条件所限。浏览器特定的代码，只能在某些生命周期钩子函数 (lifecycle hook) 中使用；一些外部扩展库 (external library) 可能需要特殊处理，才能在服务器渲染应用程序中运行。
2. 涉及构建设置和部署的更多要求。与可以部署在任何静态文件服务器上的完全静态单页面应用程序 (SPA) 不同，服务器渲染应用程序，需要处于服务端运行环境。
3. 更多的服务器端负载。

### 服务器端渲染 vs 预渲染 (SSR vs Prerendering)

如果你调研服务器端渲染 (SSR) 只是用来改善少数营销页面（例如 `/`, `/about`, `/contact` 等）的 SEO，那么你可能需要预渲染。无需使用 web 服务器实时动态编译 HTML，而是使用预渲染方式，在构建时 (build time) 简单地生成针对特定路由的静态 HTML 文件。优点是设置预渲染更简单，并可以将你的前端作为一个完全静态的站点。

> Blazor Server 支持 Prerendering

### Blazor 该选 Web Assembly 还是 Server

看一下.Net Conf 2021 大会上的一张图：

![](https://img1.dotnet9.com/2021/12/1906.png)

总结一下：

1. Server 要持久化长连接，有更高的 UI 延迟
2. Web Assembly 则是更大的下载大小，和更慢的运行时性能

## 我们在做什么

又是一个老生常谈的问题，.Net 的覆盖面太广了也导致很难解决所有问题。我们在权衡利弊之后是不是可以为.Net 生态共建出自己小小的一份力呢？

### 开源的组件库

再回到组件库上，目前市面上的组件库其实不少了，何必要继续在这个泥潭里插一脚呢？

开发过组件库的同学，或者贡献过源码的同学应该都体会到了，写一个组件是多么的复杂。而大家对于一个设计的审美角度也是不同的。当你喜欢的设计没有提供实现怎么办？从头写吗，那太累了，所以我们尝试了一件事情。

先看一下大概思路：

![架构图](https://img1.dotnet9.com/2021/12/1907.png)

简单的剖析一下：

1. 在 Blazor 的基础上，构建一个新的组件库叫 Blazor Component

那他有哪些特性呢？

- 只提供交互，不提供样式
- 标准化 Dom 结构
- 开放几乎所有可以自定义的 Slots（插槽，概念引自 Vue），允许你替换 Slots 的 Dom

2. 插槽与交互的统一单元测试（目前正在 38%，短期目标是 80%，长期目标是 90%+）

3. 基于 Material Design（样式引自 Vuetify）的示例项目，可以达到生产可用（我们自己的公司在用，也是世界五百强企业在用）

4. 快速实现新的组件库，只需要基于某个 Design + 样式控制属性 + 特殊交互即可

未来是值得憧憬的，我们希望未来是这样的：

> 惭愧，蹭了一波字节的热度

![](https://img1.dotnet9.com/2021/12/1908.jpg)

### MASA Blazor

基于 Blazor Component 和 Material Design 的 Blazor 组件库

1. 截止目前共 68 个基础组件，后续会继续增加
2. 预置组件，提供与.Net 功能深度集成且常用组合类组件，如 Url、面包屑、菜单三联动，高级搜索，i18n 等
3. MASA Blazor Pro 提供多种常见场景的预设布局
4. 全职团队维护，Issue 快速响应
5. 知名企业在用，未来 MASA Stack 产品线也将一直使用，持续增加新功能
6. 免费、开源

我们还计划未来支持一键切换主题（代码切换已经提供）、预置布局、数据展示类组件、WorkFlow 类组件等。

### MASA Blazor Pro

基于 MASA Blazor 提供的 Admin 模板，先放几张设计稿吧，源码会跟 MASA Blazor 一起放出。

![](https://img1.dotnet9.com/2021/12/1909.png)

![](https://img1.dotnet9.com/2021/12/1910.png)

![](https://img1.dotnet9.com/2021/12/1911.png)

![](https://img1.dotnet9.com/2021/12/1912.png)

![](https://img1.dotnet9.com/2021/12/1913.png)

![](https://img1.dotnet9.com/2021/12/1914.png)

### MASA EShop

基于 MASA Framework 搭建的 EShopOnDapr，将会使用 MASA Blazor Pro 提供完整的前后端示例

![eshop](https://img1.dotnet9.com/2021/12/1915.png)

开源地址：[https://github.com/masalabs/MASA.EShop](https://github.com/masalabs/MASA.EShop)

##总结

说到底没有完美的技术，在你权衡利弊之后在正确的场景使用它就是最合适的。

是春天还是寒冬也不重要，重要的是当下这一刻，它是否解决了你的痛点。

最后，一个小小的广告

**MASA Blazor 即将发布**，敬请期待，如果你对我们的组件库感兴趣，无论是代码贡献、使用、提 Issue，欢迎联系我们

![](https://img1.dotnet9.com/2021/12/1916.png)

## 参考

1. https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor
2. https://docs.microsoft.com/zh-cn/aspnet/core/blazor/state-management?view=aspnetcore-6.0&pivots=server#persist-state-across-circuits
3. https://sec.ch9.ms/ch9/daba/468d5211-982b-4c86-8b51-e1c8824edaba/dotNETConfNewBlazorWebAssembly_high.mp4
4. https://developer.mozilla.org/zh-CN/docs/WebAssembly
5. https://ssr.vuejs.org/zh/

欢迎加入技术交流群: [MASA Stack](https://qm.qq.com/cgi-bin/qm/qr?k=YqUI_NM-WCWkWxbFpGhdMMx6ghDzUEhH&jump_from=webapi)
