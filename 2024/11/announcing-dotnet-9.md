---
title: .NET 9正式发布
slug: announcing-dotnet-9
description: .NET 9正式发布了！这是.NET迄今为止生产力最高、最现代化、最安全、最智能且性能最强的版本。
date: 2024-11-03 01:14:36
lastmod: 2024-11-03 02:47:19
copyright: Reprinted
banner: false
author: .NET Team
originalTitle: Announcing .NET 9
originalLink: https://devblogs.microsoft.com/dotnet/announcing-dotnet-9/
draft: false
cover: https://img1.dotnet9.com/2024/11/0701.png
categories: 
    - .NET
tags: 
    - .NET 9
    - 正式发布
---

> 本文由AI助力翻译，站长校验。

今日，我们满怀激动地宣布，.NET 9正式发布了！这是.NET迄今为止生产力最高、最现代化、最安全、最智能且性能最强的版本。它凝聚着全球数千名开发者又一年的心血，此次新版本包含了数千项性能、安全以及功能方面的改进。你会发现，从编程语言、开发工具到工作负载，整个.NET技术栈都有了全方位的增强，让你能够基于统一平台进行开发，并轻松地为应用注入人工智能元素。

![点击查看带有工作负载、工具、生态系统和操作系统的.NET概览](https://img1.dotnet9.com/2024/11/0701.png)

.NET 9的下载资源，以及针对Visual Studio 2022和适用于Visual Studio Code的C#开发工具包的更新版本，现在均已开放获取。

[🔽 下载.NET 9](https://aka.ms/get-dotnet-9)

[获取Visual Studio 2022 v17.12版本](https://visualstudio.microsoft.com/download)

.NET团队、合作伙伴以及.NET社区将在[.NET Conf 2024](https://www.dotnetconf.net/)（为期三天的免费线上开发者活动，举办时间为11月12日至14日）上展示.NET 9的新特性，并深入剖析各项功能。快来加入我们吧！

下面让我们一同来看看.NET在开发语言、工作负载以及工具方面的亮点内容。

### 无与伦比的性能——应用运行更快，内存占用更低
.NET 9是.NET至今性能最强的版本，运行时、工作负载以及语言层面共有超过1000项与性能相关的变更，其采用的更高效算法能够生成更优质的代码。斯蒂芬·图布（Stephen Toub）对[.NET 9性能改进内容](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-9/)进行了深入剖析，那是一篇必读文章，不过在此我们还是先来讨论一下该版本中的部分亮点。

服务器垃圾回收（Server GC）机制进行了重大调整，它将依据应用的内存需求进行自适应调节，而不再取决于环境（机器、虚拟机或容器）中可用的资源（内存和CPU）。这种调整方式在多核环境下影响深远，特别是在应用内存较小或者随时间变化幅度较大的情况下。在此之前，工作站（Workstation）和服务器垃圾回收机制的实现会产生截然不同的结果，用户需要在二者之间权衡抉择。对于那些使用工作站垃圾回收机制来控制云应用内存使用的用户来说，此次调整应该颇具吸引力。内存方面的优化可能会带来一定的吞吐量成本，但这一成本或许并不明显。服务器垃圾回收机制可配置为使用旧版实现，这在测试时可能会派上用场。

TechEmpower基准测试在.NET 9面前也相形见绌，.NET 9实现了更高的吞吐量，同时内存使用量大幅下降。内存使用量下降得益于服务器垃圾回收机制的变更。

![图表展示每秒请求数（RPS）提高15%，内存使用量降低93%](https://img1.dotnet9.com/2024/11/0702.png)

[运行时](https://learn.microsoft.com/dotnet/core/whats-new/dotnet-9/overview#net-runtime)重新引入了[向量化](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-9/#vectorization)，增加了对新芯片架构的支持，涵盖了Arm64可伸缩向量扩展（SVE）、英特尔高级矢量扩展10（AVX10），并对运行时进行了硬件加速。RyuJIT提升了[Arm64](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-9/#arm64)、[循环](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-9/#loops)、[基于配置文件引导的优化（PGO）](https://learn.microsoft.com/dotnet/core/whats-new/dotnet-9/runtime#pgo-improvements-type-checks-and-casts)以及[边界检查](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-9/#bounds-checks)等方面的性能。得益于采用了原生提前编译（Native AOT）所使用的异常模型，异常处理速度提升了50%。

动态的基于配置文件引导的优化（PGO）已更新，能够对更多代码模式进行优化。现在，即时编译器（JIT）会为应用中常见以及未曾出现过的类型转换生成快速路径代码。像`(IFoo)myFoo`和`myFoo is IFoo`这类类型转换在C#中极为常见。此外，对于所观察到的常见缓冲区长度，它还能够展开并向量化对缓冲区执行的部分操作。尽管这一变更需要禁用“ReadyToRun”功能，但能使执行速度提高70%。

LINQ在多种常见场景下也进行了优化。当基础数组、集合或者可枚举对象为空时，诸如`Take`和`DefaultIfEmpty`这类方法的返回速度如今可提升多达10倍。`Enumerable.SequenceEqual`方法已经针对数组输入进行了特殊处理，它会将操作委托给`MemoryExtensions.SequenceEquals`方法，通过将数组作为 spans 传递，实现高效迭代和向量化操作。现在，`List<T>`也具备了这一能力。

`System.Text.Json`的底层细节得到了显著优化，各项操作的性能提升幅度超过50%。`JsonProperty.WriteTo`方法现在能够直接写入UTF8字节，从而避免了字符串分配操作。新的`JsonMarshal.GetRawUtf8Value`应用程序编程接口（API）会返回UTF8字节，作为`JsonElement.GetRawText`（返回字符串且需要编码和分配内存）的替代方案。如果能从给定的可枚举对象中获取元素数量，`JsonObject`现在可以正确设定其底层支持存储的大小，从而避免分配内存以及调整大小所产生的成本。

除了这些对.NET的基础性改进之外，你会发现所有类型应用的性能均有所增强，不妨继续往下阅读以了解更多详情。

### .NET Aspire——构建更优应用的基础组件

[.NET Aspire](https://learn.microsoft.com/dotnet/aspire/)是一组功能强大的工具、模板以及软件包，旨在助力无缝开发可观测且可用于生产环境的应用程序。距离.NET Aspire首次发布仅仅过去了六个月，我们已经对整个技术栈的各个部分进行了改进，从遥测和指标仪表板中的新特性，到云应用更精简的部署流程，都有涉及。看到各类应用纷纷采用.NET Aspire，以及社区根据自身场景接纳相关集成与工具，着实令人欣喜。此外，我们看到微软内部也有大量应用采用了这一技术，例如Xbox团队和Copilot团队都已将.NET Aspire集成到现有服务中，借助易于获取的洞察信息以及各种兼容的Azure集成，进一步优化了内部开发流程。

![.NET Aspire概览](https://img1.dotnet9.com/2024/11/0703.png)

此次发布的.NET Aspire 9带来了一些大家呼声很高的功能，可助力简化应用开发流程。现在，你能够通过仪表板启动和停止资源，在调试会话之间保持容器处于运行状态，还可以访问包括`WaitFor`在内的新API，以便更好地管理资源启动过程。我们与社区紧密合作，为OpenAI、Ollama、Milvus等众多平台推出了全新的无缝[集成功能](https://learn.microsoft.com/dotnet/aspire/fundamentals/integrations-overview)。我们简化了获取.NET Aspire的流程，使其更易于融入应用当中，同时改进了与Azure容器应用相关的部署场景，并且很高兴地宣布，现已为Azure函数提供.NET Aspire预览版支持。

![.NET Aspire 9新特性概览](https://img1.dotnet9.com/2024/11/0704.png)

.NET Aspire还有诸多内容等待你去探索，涵盖了从工具到更广泛的生态系统，比如全新的[.NET Aspire社区工具包](https://github.com/communitytoolkit/aspire)。如果你打算开始使用.NET Aspire，不妨看看我们在[微软学习平台（Microsoft Learn）上提供的免费课程](https://learn.microsoft.com/training/browse/?expanded=dotnet&products=dotnet-aspire)以及[新的.NET Aspire认证](https://aka.ms/aspire-credential)，它们将助力你开启.NET Aspire之旅。当然，也请让我们知晓你对.NET Aspire的看法，你可以通过[GitHub](https://github.com/dotnet/aspire)、[.NET Discord](https://aka.ms/dotnet-discord)或者我们的[直播流](https://dotnet.microsoft.com/live/community-standup)反馈给我们。

### 人工智能——蓬勃发展的人工智能生态系统
我们持续拓展.NET在构建以及为应用注入人工智能方面的能力。现在有了新的学习资料和示例，与生态系统的集成也更加简化，通过与合作伙伴协作打造出了充满活力的人工智能社区，而且将人工智能解决方案部署到云端也变得前所未有的顺畅。各行各业的众多公司都已采用.NET来为客户打造一流的人工智能体验，其中包括H&R Block、Blip以及毕马威（KPMG）等。你所喜爱的编码助手GitHub Copilot就是由.NET驱动的，而全新的微软Copilot体验也是基于.NET全新构建的。

在开发人工智能服务和应用时，开发者能够获取最新进展至关重要。正因如此，我们与Azure、OpenAI、LlamaIndex、Qdrant、Pinecone、Milvus、AutoGen、OllamaSharp、ONNX Runtime等众多人工智能生态系统中的合作伙伴携手合作，为.NET开发者提供了一系列强大的功能选项。

![展示.NET人工智能生态系统中各类库和组件的概览图](https://img1.dotnet9.com/2024/11/0705.png)

我们还通过与社区以及控件供应商合作伙伴共同构建智能组件生态系统，让将融入人工智能的控件集成到.NET应用中变得更加容易。

#### .NET的人工智能构建模块

一个强大的生态系统意味着开发者在选择最适合自身场景的方案时，拥有了比以往更多的选择。我们思考了如何简化这些集成流程，并消除因生态系统中应用程序编程接口（API）和功能数量不断增长所带来的准入障碍。为此，我们与语义内核（Semantic Kernel）展开合作，在[Microsoft.Extensions.AI](https://devblogs.microsoft.com/dotnet/introducing-microsoft-extensions-ai-preview/)和[Microsoft.Extensions.VectorData](https://devblogs.microsoft.com/dotnet/introducing-microsoft-extensions-vector-data/)项目下，为.NET生态系统引入了一组抽象概念，它们为与人工智能服务（例如小型和大型语言模型、嵌入向量、向量存储以及中间件）进行交互提供了统一的C#抽象层。这种简化后的新方法已经在早期采用者（如[Pieces](https://pieces.app/)和[OllamaSharp](https://github.com/awaescher/OllamaSharp)）那里取得了良好的成效。

![解释人工智能扩展工作原理的示意图](https://img1.dotnet9.com/2024/11/0706.png)

#### Tensors和Tokenizers
Microsoft.Extensions.AI和VectorData只是我们在.NET 9中所提供构建模块的一部分，我们还对相关库和基础数据类型进行了重大改进，以推动人工智能开发。`Microsoft.ML.Tokenizers`针对包括GPT（4、o1等）、Llama、Phi以及Bert在内的热门模型系列，在分词器方面进行了改进，此外还新增了对字节级字节对编码（Byte-Level BPE）、SentencePiece以及WordPiece等分词算法的支持。

随着语言模型即服务的出现，开发者使用人工智能的门槛大幅降低。`Tensor<T>`也为人工智能开发带来了助力，它作为一种新的数据类型，可用于表示多维数据，从而简化了库之间的交互操作以及相关运算的应用。

我们迫不及待地想看看你利用这些新的构建模块集成功能能够创造出什么样的成果。若想快速上手，可以浏览我们为.NET提供的人工智能文档以及示例代码。

### 面向.NET开发者的GitHub Copilot增强功能
[GitHub Copilot](https://aka.ms/githubcopilot)通过在编辑器体验方面提供更优质的结果，并在.NET开发者的常规工作流程中提供人工智能辅助，从而提升了开发效率。随着Visual Studio和Visual Studio Code的最新版本发布以及GitHub Copilot的更新，这一体验变得更加出色。开启Copilot后，集成开发环境（IDE）中处处融入了人工智能，可在开发者生命周期的各个环节（从编写代码、编写测试到调试应用）为你提供帮助。以下是在最新版本中你可以期待的部分功能：

- **人工智能智能变量检查**：通过集成人工智能变量检查功能，优化调试工作流程。
- **人工智能驱动的可枚举对象可视化工具**：在可枚举对象可视化工具中提供人工智能驱动的可编辑语言集成查询（LINQ）表达式。
- **借助GitHub Copilot修复代码**：GitHub Copilot能够协助你解决代码问题。
- **针对C#的更优人工智能补全功能**：GitHub Copilot会从相关源文件中引入更多上下文信息，以改进C#代码的补全效果。
- **借助GitHub Copilot调试测试**：通过使用“借助GitHub Copilot调试测试”功能，获取调试失败测试的帮助。

<video class="wp-video-shortcode" id="video-54480-1" width="976" height="504" preload="metadata" controls="controls"><source type="video/mp4" src="https://devblogs.microsoft.com/dotnet/wp-content/uploads/sites/10/2024/11/copilotvs2022.mp4?_=1" /><a href="https://devblogs.microsoft.com/dotnet/wp-content/uploads/sites/10/2024/11/copilotvs2022.mp4">https://devblogs.microsoft.com/dotnet/wp-content/uploads/sites/10/2024/11/copilotvs2022.mp4</a></video>

### 使用ASP.NET Core和Blazor进行全栈式Web开发
[ASP.NET Core](https://learn.microsoft.com/aspnet/core)是我们用于.NET的全栈式Web框架，提供了构建现代化Web应用以及可扩展后端服务所需的一切功能。.NET增添了众多新特性，在性能、可访问性以及安全性方面均有显著改进。使用.NET 9构建的ASP.NET Core应用默认具备安全性，对提前编译（AOT）的支持得到了扩展，监控和跟踪功能也有所改善，再加上内置的性能提升，应用将实现更高的吞吐量、更快的启动时间，并且内存使用量更低。

![.NET 9中的ASP.NET Core——质量、开发体验、云原生特性](https://img1.dotnet9.com/2024/11/0707.png)

#### ASP.NET Core中静态文件的优化处理
静态Web资源（如JavaScript和CSS文件）几乎是每个Web应用的组成部分。.NET 9中的ASP.NET Core现在会在构建和发布过程中对这些文件进行优化，以实现高效部署。在构建阶段，ASP.NET Core会识别所有静态Web资源，并通过在文件名中添加基于内容的哈希值来生成带有指纹标识的文件版本。这种指纹机制确保了文件名的唯一性，避免使用旧版本文件，并能让文件得以积极缓存。在应用发布时，这些文件还会使用Brotli算法进行预压缩，这极大地减小了文件下载大小，同时减轻了服务器的压缩工作负担。运行时会通过端点路由来处理这些文件，这意味着你现在可以针对静态文件使用其他具备端点感知能力的功能，例如按端点进行授权。

#### .NET 9中Blazor的改进
在.NET 9中，Blazor的功能比以往更强大，能够助力你构建美观的现代化Web及混合应用程序。此次发布为Blazor的各个方面都带来了性能提升，新增了Blazor混合应用和Web应用模板，还为开发者提供了新的应用程序编程接口（API），以便打造令人满意的用户体验。

Blazor现在能够在运行时通过新的`RendererInfo`应用程序编程接口（API）检测组件的渲染模式，并相应地调整组件渲染方式。在预渲染期间，你可以禁用或隐藏交互元素，待组件具备交互性后再启用它们。

示例代码如下：

```razor
@if (RendererInfo.IsInteractive)
{
    <button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
}
else
{
    <p>One moment, please</p>
}
```

使用交互式服务器端渲染（Blazor Server）的Blazor应用会受益于全新的重新连接体验，其用户界面更加友好，能够更快地重新连接到服务器，并且在用户连接丢失时会自动处理页面重新加载操作。

#### ASP.NET Core中OpenAPI的增强功能
全球范围内，众多大规模服务都是基于使用ASP.NET Core构建的应用程序编程接口（API），并且我们在每一次发布时都会持续改进构建这些API的体验。对于API开发者来说，.NET 9中的一大亮点是通过`Microsoft.AspNetCore.OpenAPI`软件包新增了对OpenAPI文档生成的内置支持。元数据会自动从应用代码、属性以及扩展方法中提取出来。之后，还可以使用针对操作、模式或者整个文档进行操作的转换器，对文档做进一步定制。在最小化API应用中，该功能对原生提前编译（Native AOT）友好，使你能够针对最佳性能对应用进行优化。除此之外，OpenAPI文档可以在构建时生成，并集成到利用OpenAPI工具的本地开发工作流程以及构建管道当中。

![展示新的API、文档以及测试情况的示意图](https://img1.dotnet9.com/2024/11/0708.png)

#### ASP.NET Core中安全性的改进
安全性依旧是ASP.NET Core的核心关注点，你会发现多项改进措施，有助于确保应用默认具备安全性。现在，在Linux系统上设置受信任的开发证书变得更加容易，便于在开发过程中启用超文本传输安全协议（HTTPS）。Blazor现在内置了用于将认证状态传递到客户端的应用程序编程接口（API），还支持为OAuth和开放ID连接（OIDC）授权请求添加额外参数，并提供了对推送授权请求（PAR）的支持。最后，我们强化了ASP.NET Core的数据保护功能，并改进了Kestrel的连接指标，以便更轻松地检测连接失败的原因。

![.NET 9中ASP.NET Core新特性概览](https://img1.dotnet9.com/2024/11/0709.png)

### .NET MAUI——增强多平台应用开发

[.NET MAUI](https://learn.microsoft.com/dotnet/maui)是使用.NET跨移动端和桌面端构建多平台应用的绝佳方式。它除了提供统一的抽象层，能通过单一应用程序编程接口（API）访问原生功能，并基于单一代码库创建出色的跨平台用户界面外，还包含了基于.NET访问原生API来构建适用于安卓（Android）、苹果iOS、苹果macOS以及Windows系统应用的基础功能。进入.NET 9阶段，.NET MAUI的首要目标就是提升质量和可靠性，以便你能更轻松地将应用投入生产环境。过去一年里，我们看到在谷歌应用商店（Google Play Store）中，使用.NET MAUI构建的应用数量增加了一倍多，开发者的使用率增长超过30%，达到历史最高水平，而且社区的参与度和贡献也十分惊人。

![展示.NET MAUI使用量和拉取请求（PRs）增长情况的图片](https://img1.dotnet9.com/2024/11/0710.png)

最近，我们欢迎[Syncfusion](https://www.syncfusion.com/)（.NET生态系统中领先的组件供应商）为.NET MAUI做出贡献。自今年7月至9月，Syncfusion开始为.NET MAUI贡献代码以来，其贡献量占社区总贡献量的比例超过了55%，与前三个月相比，增长了557%，这得益于其原本就非常出色的贡献成果。在.NET 9中，我们将社区放在核心位置，引入了一个全新的项目模板，其中包含14个免费且开源的Syncfusion控件以及来自社区的其他热门库，展示了针对模型 - 视图 - 视图模型（MVVM）、数据库访问、导航、视图刷新以及其他常见应用模式的推荐实践方法。你可以利用这个模板快速开启应用开发之旅。

![展示使用.NET MAUI开发的移动端和桌面端应用示例的图片](https://img1.dotnet9.com/2024/11/0711.png)

我们一直倾听开发者的心声，.NET 9为桌面端和移动端应用带来了性能提升、可靠性增强以及更深入的集成功能。你会发现.NET MAUI在各方面都有显著的性能改进，包括针对iOS和Mac Catalyst系统的`CollectionView`（集合视图）与`CarouselView`（轮播视图）的全新实现，对现有控件和应用生命周期的更新，以及原生提前编译（Native AOT）和剪裁功能的增强，这些都有助于你构建体积更小、运行速度更快的应用。除了支持最新的iOS、macOS和安卓操作系统外，我们在.NET 9中还新增了多项原生平台功能，例如安卓资源包（Android Asset Packs）、与原生库交互的改进，以及通过新的[Xcode Sync dotnet工具](https://learn.microsoft.com/dotnet/maui/macios/xcsync)实现Xcode与Visual Studio Code之间更流畅的集成。

![.NET 9中.NET MAUI特性概览](https://img1.dotnet9.com/2024/11/0712.png)

.NET 9中的.NET MAUI有诸多值得喜爱和探索的内容。一定要仔细查看[新特性文档](https://learn.microsoft.com/dotnet/maui/whats-new/dotnet-9)，并尝试使用新的项目模板哦。

### 使用.NET 9进行Windows开发
借助.NET 9，你的Windows应用将能够使用最新的操作系统特性和功能，同时确保它们比以往任何时候都更具性能且更易于访问。无论你是使用[WinUI 3](https://learn.microsoft.com/windows/apps/winui/winui3/)和[Windows应用软件开发工具包（Windows App SDK）](https://learn.microsoft.com/windows/apps/windows-app-sdk/)来开发新的现代化应用，还是对现有的[Windows Presentation Foundation（WPF）](https://learn.microsoft.com/dotnet/desktop/wpf/)以及[Windows Forms](https://learn.microsoft.com/dotnet/desktop/winforms/)应用进行现代化改造，你的Windows应用在.NET 9上都能实现最佳运行效果。我们一直与Windows开发者社区紧密合作，带来了大家一直期待的功能。这其中包括为WinUI 3提供原生提前编译（Native AOT）支持，以打造体积更小且性能更优的应用；为WPF引入流畅用户界面（Fluent UI）相关的现代化主题增强功能；为WinForms增添了暗黑模式、现代化图标应用程序编程接口（API），并通过`Control.InvokeAsync`改进了异步API访问功能。

![.NET 9中Windows开发者特性概览](https://img1.dotnet9.com/2024/11/0713.png)

在.NET 9中针对Windows开发还有很多内容值得探索，所以一定要仔细阅读[WinUI 3](https://learn.microsoft.com/windows/apps/windows-app-sdk/stable-channel)、[WPF](https://learn.microsoft.com/dotnet/desktop/wpf/whats-new/net90)以及[WinForms](https://learn.microsoft.com/dotnet/desktop/winforms/whats-new/net90)的新特性文档。

### C#和F#——你喜爱的编程语言变得更加出色
[C#](https://learn.microsoft.com/dotnet/csharp/)是全球最受欢迎的编程语言之一。在C# 13版本中，我们着重关注了一些特性，它们能让你以熟悉且喜爱的编程风格更轻松、更安全、更快速地编写代码。在方法签名中使用`params`修饰符的功能在C# 13中通过新增的集合表达式得到了增强。这意味着你不再局限于将`params`与数组类型搭配使用，而是可以使用任何受支持的集合类型了。

C# 13通过引入使用`ref struct`值的新方式解锁了更多高性能代码，并且借助`System.Threading.Lock`让处理多线程应用变得更加容易。

示例代码如下：

```csharp
Lock myLock = new();

void Concat<T>(params List<T> items)
{
    lock (myLock)
        Console.WriteLine(string.Join("\e[1mItem: \e[0m", items));
}
```

[F#](https://learn.microsoft.com/dotnet/fsharp/)一如既往地为.NET开发者提供优质的函数式编程体验。F# 9带来了各种语言、库以及工具方面的增强功能，旨在让你的程序更安全、更具弹性且性能更佳。可空引用类型为与C#库的交互带来了类型安全性，优化后的整数范围加速了循环和其他推导式的执行速度，优化后的相等性检查避免了装箱操作，并提升了许多常见操作的性能。区分联合类型会自动生成`.Is*`属性，便于快速进行情况测试。标准库现在包含了针对集合的随机函数，这在数据科学和游戏开发中非常实用。开发者的工作效率也因诊断功能、解析器恢复以及各种工具改进而得到了提升。

示例代码如下：

```fsharp
// FS3261: 空值警告：类型'string' 和'string | null' 的可空性不一致。
let methodArgument (s: string | null) = File.Create s
let matchNullableString(s: string | null) =    
        match s with  // `s` 是 string | null 类型
        | null -> 0
        | notNull -> notNull.Length // `notNull` 是 string 类型
```

还有很多精彩特性等着你去发现，所以一定要浏览[C# 13新特性文档](https://learn.microsoft.com/dotnet/csharp/whats-new/csharp-13)以及[F# 9新特性文档](https://learn.microsoft.com/dotnet/fsharp/whats-new/fsharp-9)哦。如果你刚开始踏上C#开发之旅，不妨查看一下与freeCodeCamp合作推出的[基础C#认证](https://aka.ms/csharp-certification)。

### 全球顶尖的开发者工具
伴随.NET 9的发布，我们的开发者工具也进行了更新，这将使你的开发效率达到前所未有的高度。首先，[Visual Studio 2022 17.12版本](https://aka.ms/vs/v1712GA)的发布为开发者工作流程的各个方面都带来了一系列效率提升，包括显著的性能增强、更出色的调试与诊断体验、与.NET Aspire更紧密的集成、更深入的云集成、对C# 13的分析器支持、增强的Git支持等等！实际上，此次Visual Studio 2022版本所包含的用户期待功能比以往任何时候都要多。这个版本中总有适合你的功能，相信你一定会喜欢它的。

<video class="wp-video-shortcode" id="video-54480-2_html5" width="1064" height="600" preload="metadata" src="https://devblogs.microsoft.com/dotnet/wp-content/uploads/sites/10/2024/11/vs20221712.mp4?_=2" style="box-sizing: border-box; font-family: Helvetica, Arial; max-width: 100%; display: inline-block; width: 995.281px; height: 561.249px;"></video>

适用于Visual Studio Code的C#开发工具包也在持续演进，编辑可靠性得到了提高，NuGet包管理功能有所增强，测试适配器和代码覆盖率结果更优，对.NET MAUI开发的支持也得到了改进，项目启动/调试配置也进行了升级。立即下载最新版本的[Visual Studio 2022](https://visualstudio.microsoft.com/downloads/)和[适用于Visual Studio Code的C#开发工具包](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csdevkit)，让你的开发工作流程从中受益并得到改进吧。

我们知道开发者喜爱命令行界面（CLI），因此我们一直在努力改善.NET CLI的使用体验，并助力其成为安全开发方法的一部分。在.NET 9中，我们对终端日志记录器进行了全面改进，包括添加可点击链接、时长计时器、颜色编码等等。日志记录器的输出更加简洁，而且现在在构建结束时你会看到失败和警告总数的汇总信息，使其比以往更具实用性。`dotnet restore`命令也已更新，能够[审核顶级和传递依赖项](https://learn.microsoft.com/nuget/concepts/auditing-packages)是否存在软件包漏洞。这一功能与[集中式软件包管理](https://learn.microsoft.com/nuget/consume-packages/Central-Package-Management)配合默契，使你能够快速将软件包升级部署到代码仓库内的所有项目中。`dotnet restore`会在终端、Visual Studio以及持续集成/持续交付（CI/CD）管道中基于[GitHub安全公告数据库](https://github.com/advisories/)中的软件包漏洞信息向你发出提醒。

![展示.NET CLI新特性亮点的图片](https://img1.dotnet9.com/2024/11/0714.png)

.NET软件开发工具包（SDK）中还有很多你会喜欢的内容，所以一定要仔细阅读[新特性文档](https://learn.microsoft.com/dotnet/core/whats-new/dotnet-9/sdk)哦。

### 蓬勃发展的创作者与贡献者社区
我们热爱出色的.NET社区，没有你们的支持和贡献，就不会有.NET 9的诞生。我们收到了来自9000多名社区成员的超过26000次贡献！感谢你们提出的每一个问题、每一条评论、每一次代码审查以及每一个拉取请求，正是你们的付出，才让这个版本成为.NET迄今为止最优秀的版本。我们也很欣慰看到大家对.NET的喜爱，在今年的[Stack Overflow开发者调查](https://survey.stackoverflow.co/2024/technology/#1-other-frameworks-and-libraries)中，.NET与ASP.NET Core一同被评为最受推崇的框架，C#也被列为顶尖的Web框架和编程语言之一。感谢你们助力构建了这个令人惊叹的全球开发者社区。

![带有感谢字样且背景有头像的图片](https://img1.dotnet9.com/2024/11/0715.png)

感谢所有帮助.NET生态系统蓬勃发展的库创建者，是你们让[NuGet](https://nuget.org/)成为了每年增长速度最快的软件包生态系统。如今，.NET开发者可以使用的软件包已超过42万个，它们的下载次数总计超过5700亿次，这一数字令人惊叹。我们正在大力投入，不断完善NuGet，添加开发者喜爱的功能，并努力使其成为全球最安全的软件包生态系统。

![NuGet生态系统概览](https://img1.dotnet9.com/2024/11/0716.png)

NuGet为整个生态系统中的库创建者和使用者都带来了重大改进。NuGet.org网站焕然一新，现在支持暗黑模式，还与GitHub合作，为Dependabot添加了[原生NuGet支持](https://github.blog/changelog/2023-11-28-improvements-to-nuget-support-for-dependabot/)，支持通过开源安全基金会（OpenSSF）实现[开源计分卡和固定依赖项](https://devblogs.microsoft.com/nuget/openssf-scorecard-for-net-nuget/#running-scorecard-on-a-nuget-package)功能，并增强了与[deps.dev](https://deps.dev/)的集成，以便更深入地了解项目中的依赖关系。

### 立即使用.NET 9开启开发之旅
现在就加入我们在[.NET Conf](https://dotnetconf.net/)的线上直播活动吧，聆听.NET团队成员现场讲解最新的增强功能。

.NET 9的下载资源以及针对Visual Studio 2022和适用于Visual Studio Code的C#开发工具包的更新版本现在均已开放获取。

[🔽 下载.NET 9](https://aka.ms/get-dotnet-9)

[获取Visual Studio 2022 v17.12版本](https://visualstudio.microsoft.com/download)

想要深入了解本版本中各个部分的新特性，可以查看.NET 9各部分的最新文档：
- [.NET 9新特性](https://learn.microsoft.com/dotnet/core/whats-new/dotnet-9/overview)：[运行时](https://learn.microsoft.com/dotnet/core/whats-new/dotnet-9/runtime)、[库](https://learn.microsoft.com/dotnet/core/whats-new/dotnet-9/libraries)以及[软件开发工具包（SDK）](https://learn.microsoft.com/dotnet/core/whats-new/dotnet-9/sdk)
- [C# 13新特性](https://learn.microsoft.com/dotnet/csharp/whats-new/csharp-13)
- [F# 9新特性](https://learn.microsoft.com/dotnet/fsharp/whats-new/fsharp-9)
- [ASP.NET Core新特性](https://learn.microsoft.com/aspnet/core/release-notes/aspnetcore-9.0)
- [.NET Aspire新特性](https://learn.microsoft.com/dotnet/aspire/whats-new/)
- [.NET MAUI新特性](https://learn.microsoft.com/dotnet/maui/whats-new/dotnet-9)
- [实体框架核心（EF Core）新特性](https://learn.microsoft.com/ef/core/what-is-new/ef-core-9.0/whatsnew)
- [Windows Presentation Foundation（WPF）新特性](https://learn.microsoft.com/dotnet/desktop/wpf/whats-new/net90)
- [Windows Forms新特性](https://learn.microsoft.com/dotnet/desktop/winforms/whats-new/net90)

此外，在接下来的几个月里，我们还会发布关于.NET 9中涵盖语言、工作负载以及工具等各方面新特性的深度剖析博客文章，所以一定要订阅.NET博客，这样当新文章发布时你就能及时收到通知啦。

我们迫不及待地想看看你使用.NET 9开发出什么样的成果呢。 