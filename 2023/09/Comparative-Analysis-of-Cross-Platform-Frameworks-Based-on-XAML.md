---
title: 【译】基于XAML的跨平台框架对比分析
slug: Comparative-Analysis-of-Cross-Platform-Frameworks-Based-on-XAML
description: 多年来，基于XAML的UI框架已经有了很大的发展。这些框架主要包含：支持跨平台应用的Avalonia UI, Uno Platform和 .NET MAUI。如果微软早点推出一个类似Flutter这样的跨平台UI框架，我们可能就不会有这么多的选择。
date: 2023-09-09 21:40:18
lastmod: 2023-09-21 22:15:23
copyright: Reprint
author: czwy
originaltitle: 【译】基于XAML的跨平台框架对比分析
originallink: https://www.cnblogs.com/czwy/p/17572226.html
draft: false
cover: https://img1.dotnet9.com/2023/09/cover_04.png
categories: .NET相关
---

多年来，基于XAML的UI框架已经有了很大的发展，下面的图表是最好的说明。这些框架主要包含：支持跨平台应用的Avalonia UI, Uno Platform和 .NET MAUI。事实上，除了Avalonia UI之外，对跨平台XAML的需求是其发展的主要驱动力。如果微软早点推出一个类似Flutter这样的跨平台UI框架，我们可能就不会有这么多的选择。这样有利有弊：好处在于我们有很多跨平台方案可以选择，坏处在于不同的框架有不同的对象模型以及各自的特有的XAML语法（dialect of XAML）。

在关注各种 .NET UI 框架时，我们会提出同一个问题：应该使用哪一个XAML UI框架来开发我们的应用？这是一个合理且重要的问题。迄今为止还没有一个明确的答案。但是，对于每个具体的应用，这个问题很容易回答，因为可以针对特定的应用需求比较分析每一种框架的优点和缺点。通过概述基于 XAML 的主要 UI 框架的优点和缺点，本文档旨在帮助公司和开发人员回答以下问题：

> 应该选择哪一个XAML框架开发我的跨平台应用？

[![img](https://img1.dotnet9.com/2023/09/cover_04.png)](https://img2023.cnblogs.com/blog/3056716/202307/3056716-20230721184841650-322714980.png)

高屋建瓴地看，可以从架构上描述这些基于XAML的跨平台UI框架的差异。这些框架都是基于相同的 .NET（以前的Mono）工具。不容忽视的是，Xamarin对 .NET 的贡献使得这些框架存在。此外，在 .NET 6+ 中，这些框架在每个平台上都使用相同的运行时和核心库。

- [**Avalonia UI**](https://github.com/AvaloniaUI/Avalonia) : 完全自己呈现控件和用户界面元素，这一点和Flutter相同。
- [**.NET MAUI**](https://github.com/dotnet/maui) : 标准化一组名称、属性、事件，并将它们应用/链接到特定平台的原生控件。如果单个平台不支持某项功能，该功能则不会出现在所有平台的MAUI中（不涉及特定平台的代码）
- [**Uno Platform**](https://github.com/unoplatform/uno) : 使用选定的几个特定于平台的基本元素来构建和渲染控件。 对于高级控件，这提供了近乎像素完美的结果。这意味着 Uno Platform 像 Avalonia UI 和 Flutter那样完全渲染控件; 不过, 它还支持直接嵌入特定平台的原生控件，是一个混合架构。

因为 WPF 和 UWP/WinUI 这些基于XAML的微软框架不是跨平台的，所以这里不进行详细比较。但是 WPF 可以通过[Wine Mono](https://github.com/madewokherd/wine-mono) 或者 [Avalonia XPF](https://avaloniaui.net/XPF)跨平台运行。WinUI/UWP已通过接下来讨论的 Uno Platform 支持跨平台运行。

**项目链接**

| Project      | Website                                              | GitHub                                                       | Docs                                                         |
| ------------ | ---------------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Avalonia UI  | [avaloniaui.net](https://avaloniaui.net/)            | [github.com/AvaloniaUI/Avalonia](https://github.com/AvaloniaUI/Avalonia) | [docs.avaloniaui.net](https://docs.avaloniaui.net/)          |
| .NET MAUI    | [maui](https://dotnet.microsoft.com/en-us/apps/maui) | [github.com/dotnet/maui](https://github.com/dotnet/maui)     | [docs.microsoft.com/en-us/dotnet/maui/](https://docs.microsoft.com/en-us/dotnet/maui/) |
| Uno Platform | [platform.uno](https://platform.uno/)                | [github.com/unoplatform/uno](https://github.com/unoplatform/uno) | [platform.uno/docs/](https://platform.uno/docs/articles/intro.html) |

**其他框架**

还有一些其他可用于 .NET跨平台开发的解决方案在本文不再详细描述。即便本文不会进行详细对比，这些框架或者解决方案也值得了解一下：

1. [WPF](https://github.com/dotnet/wpf) : 正如上文所讲，WPF 可以通过[Wine Mono](https://github.com/madewokherd/wine-mono) 或者 [Avalonia XPF](https://avaloniaui.net/XPF)跨平台运行。对于WPF代码量较大的现有应用，可以考虑这种跨平台解决方案。
2. [Eto.Forms](https://github.com/picoe/Eto) : 一个类似于 .NET MAUI 的UI框架，使用平台原生控件构建UI，XAML也可以用于序列化和构造UI。
3. [Noesis GUI](https://www.noesisengine.com/) : 用于游戏开发, Noesis GUI 重新创建WPF，用于游戏引擎（如Unity）以构建用户界面。Noesis GUI 对 XAML 的支持非常广泛，可以和 Microsoft Blend 一起使用。 如果它可以在游戏引擎之外工作，并且对较小的应用程序有更好的许可，那么它将是一项早于其他跨平台XAML实现的有趣技术。
4. [.NET MAUI + Blazor Hybrid](https://learn.microsoft.com/en-us/aspnet/core/blazor/hybrid/tutorials/maui?view=aspnetcore-7.0) : .NET MAUI 可以托管 Blazor Web 应用（在 BlazorWebView 控件内），使其更像是应用程序和服务容器。对于那些希望将现有 Web 应用程序重新打包并分发为移动应用程序的人来说，这是一个非常有吸引力的选择。
5. [.NET MAUI + Avalonia UI Hybrid](https://github.com/AvaloniaUI/AvaloniaMauiHybrid) : .NET MAUI 还可以托管 Avalonia UI(在AvaloniaView控件里)，使其更像是一个应用程序和服务的容器。 由于 Avalonia 只是一个 UI 框架，要轻松获取 .NET MAUI 的所有附加平台抽象功能 （Essentials） 以及更轻松地打包和部署移动应用时，这是一个非常有吸引力的选择。

更多时候将 .NET MAUI 作为应用程序加服务容器，然后托管其他 UI 框架（如 Blazor 或 Avalonia UI）是一个有吸引力的选择。这种架构可能会在未来获得更多的关注，绝对是一个值得密切关注的领域。

## 框架对比

每个框架都有不同的表现——在某些地方很明显。下表中重点关注具有较高影响力的领域和特征。对于所有框架表象相同的地方将不在下表中呈现（仅关注差异）。

这种比较是基于对各种框架的大量研究和经验；结果不免有些主观，还需要注意的是，. NET MAUI的使用经验是三个选项中最少的，这可能会影响排名的准确性。

- ✔️ 表示支持该特性 ❌ 表示不支持
- ⭐⭐⭐ 是最高/最好的评分, ⭐ 是最低/最差的

|                              | Avalonia | .NET MAUI | Uno Platform |
| ---------------------------- | -------- | --------- | ------------ |
| ▶ **特性**                   |          |           |              |
| MVVM 模式                    | ✔️｜⭐⭐⭐   | ✔️｜⭐⭐     | ✔️｜⭐⭐⭐       |
| MVU 模式                     | ❌        | ✔️｜⭐⭐     | ✔️｜⭐         |
| Pixel-perfect rendering      | ✔️｜⭐⭐⭐   | ❌         | ✔️｜⭐⭐        |
| 控件                         | ✔️｜⭐⭐⭐   | ❌         | ✔️｜⭐⭐⭐       |
| 样式和主题                   | ✔️｜⭐⭐⭐   | ✔️｜⭐      | ✔️｜⭐⭐⭐       |
| 支持统一的外观               | ✔️｜⭐⭐⭐   | ❌         | ✔️｜⭐⭐⭐       |
| 平台原生外观                 | ❌        | ✔️｜⭐⭐⭐    | ✔️｜⭐         |
| 平台一致性                   | ✔️｜⭐⭐⭐   | ✔️｜⭐      | ✔️｜⭐⭐        |
| 原生控件集成                 | ✔️｜⭐     | ✔️｜⭐⭐⭐    | ✔️｜⭐⭐⭐       |
| XAML 语法和代码共享          | ✔️｜⭐⭐    | ✔️｜⭐      | ✔️｜⭐⭐⭐       |
| C# 代码隐藏共享              | ✔️｜⭐⭐    | ✔️｜⭐      | ✔️｜⭐⭐⭐       |
| 热重载                       | ❌        | ✔️｜⭐⭐⭐    | ✔️｜⭐⭐⭐       |
| 第三方支持                   | ✔️｜⭐     | ✔️｜⭐⭐⭐    | ✔️｜⭐⭐        |
| 高级文本格式                 | ✔️｜⭐⭐⭐   | ❌         | ❌            |
| 非用户界面功能               | ❌        | ✔️｜⭐⭐     | ✔️｜⭐⭐⭐       |
| ▶ **Strategy & Development** |          |           |              |
| 性能（理论）                 | ⭐⭐⭐      | ⭐⭐⭐       | ⭐⭐           |
| 移动应用稳定性               | ⭐        | ⭐⭐        | ⭐⭐           |
| 桌面应用稳定性               | ⭐⭐⭐      | ⭐⭐        | ⭐            |
| 可用控件                     | ⭐        | ⭐⭐        | ⭐⭐⭐          |
| 代码许可证                   | ⭐⭐⭐      | ⭐⭐⭐       | ⭐⭐           |
| 免费支持                     | ⭐        | ⭐⭐        | ⭐⭐⭐          |
| 付费支持                     | ⭐⭐⭐      | ⭐⭐        | ⭐⭐⭐          |
| 项目速度                     | ⭐⭐       | ⭐⭐        | ⭐⭐           |
| 易于贡献                     | ⭐⭐⭐      | ⭐⭐        | ⭐⭐           |
| 代码库可读性                 | ⭐⭐⭐      | ⭐         | ⭐            |
| 开发人员体验                 | ⭐⭐⭐      | ⭐⭐        | ⭐            |
| 企业支持                     | ⭐⭐       | ⭐⭐        | ⭐⭐⭐          |
| ▶ **IDE 和集成工具**         |          |           |              |
| Visual Studio                | ✔️｜⭐⭐    | ✔️｜⭐⭐⭐    | ✔️｜⭐⭐        |
| Visual Studio Code           | ✔️｜TBD   | ✔️｜⭐⭐     | ✔️｜⭐⭐⭐       |
| Rider                        | ✔️｜⭐⭐⭐   | ✔️｜⭐      | ✔️｜⭐⭐        |
| Design tool integration      | ❌        | ❌         | ✔️｜⭐⭐        |
| ▶ **平台支持**               |          |           |              |
| iOS                          | ✔️｜⭐     | ✔️｜⭐⭐⭐    | ✔️｜⭐⭐        |
| Android                      | ✔️｜⭐     | ✔️｜⭐⭐⭐    | ✔️｜⭐         |
| Windows桌面应用              | ✔️｜⭐⭐    | ✔️｜⭐      | ✔️｜⭐⭐⭐       |
| macOS桌面应用                | ✔️｜⭐⭐⭐   | ✔️｜⭐      | ✔️｜⭐         |
| Linux桌面应用                | ✔️｜⭐⭐⭐   | ❌         | ✔️｜⭐         |
| Web Browser (WASM)           | ✔️｜⭐     | ❌         | ✔️｜⭐⭐⭐       |
| ▶ **整体平台支持**           |          |           |              |
| 移动端                       | ✔️｜⭐     | ✔️｜⭐⭐⭐    | ✔️｜⭐⭐        |
| 桌面程序                     | ✔️｜⭐⭐⭐   | ✔️｜⭐      | ✔️｜⭐         |
| Web                          | ✔️｜⭐     | ❌         | ✔️｜⭐⭐⭐       |

该表格最近一次更新是在2023年7月.

## 备注

- MVU模式

  .NET MAUI对传统上认为的带有 [C# Markup](https://learn.microsoft.com/en-us/dotnet/communitytoolkit/maui/markup/markup) and [Comet](https://github.com/dotnet/Comet)的MVU模式具有最完整的支持. 这是一个活跃的试验性和开发领域，预计未来将得到和MVVM的相同级别的支持。Uno Platform开发了自己的MVU变体，称为MVU-X。这与其他产品有很大不同，并且具有更高的学习曲线，但确实与 XAML 数据绑定集成得更好。MVU模式这一全新方法的长期可行性还有待观察，在这实验性的方案稳定之前，最好谨慎选择。Avalonia没有直接的支持MVU模式。但是，它提供了两个类库支持使用声明性语法替代XAML编写UI界面。[Avalonia.Markup.Declarative](https://github.com/AvaloniaUI/Avalonia.Markup.Declarative)通过在Avalonia上提供帮助方法和扩展来支持许多C#标记概念。这提供了一种用C#编写UI界面的好方法，该方法可以遵循MVU模式而不需要使用XAML。F# 开发人员的另一个选择是[Avalonia.FuncUI](https://github.com/fsprojects/Avalonia.FuncUI)，它专门为F#语言提供了类似的支持。值得注意的是，在所有框架中，Avalonia 对 F# 的支持最好（由社区提供）。

- Pixel-Perfect Rendering

  在这三个框架中，只有Avalonia支持真正的“pixel-perfect”渲染。这是由于架构的原因，只有Avalonia完全绘制了自己的用户界面和控件。虽然Uno Platform试图实现“pixel-perfect”，但由于使用原生的基本控件，在不同平台之间经常存在差异。Uno Platform（skia除外）从未完全绘制自己的控件，因此只是趋近于“pixel-perfect”。

- 无固定外观控件(Lookless Controls), 样式 & 主题

  当开发人员想到 XAML 时，他们通常会想到无固定外观控件（lookless controls）。能够完全更改控件的样式和默认模板以将其转换为完全不同的内容是 WPF 的一个主要功能。Avalonia和Uno Platform都完整支持自己版本的无固定外观控件（lookless controls）和模板重定义。但是，MAUI不具备此功能，仅支持更改一些常见的属性。在这方面，可以把MAUI看作是Windows Forms这类较旧的界面工具包。例如，这意味着在 MAUI 中不支持在按钮内放置图标或图形，而在其他的XAML框架中则很容易实现。什么是[Lookless Controls](https://social.technet.microsoft.com/wiki/contents/articles/29866.aspx) WPF控件的行为是固定的。例如，按钮有一组固定的事件，包括单击事件。不管你用按钮控件做什么操作，它仍然会有一个点击事件。 WPF控件没有固定的“外观”。Lookless这个词恰好可以简洁的表达这个意思。 按钮的默认外观是由默认的XAML模板定义的，可以替换一个完全不同的模板，从而完全改变按钮控件的外观。

- 平台一致性

  在使用跨平台框架进行开发时，应用程序和代码的一致性非常重要。您不想在一个平台上开发和验证的功能，然后发现它在另一个平台上的运行效果不同。在这方面，.NET MAUI 非常差，因为它链接到每个平台上的原生控件。这不仅需要对所有地方进行验证，而且需要多次编写自定义控件，同时花费大量时间调整内容以使其看起来一致（类似于让网页在所有浏览器上正确呈现）大多数情况下，Uno Platform比MAUI表现得更好。但是，它也存在一些严重的问题，一些功能并不是在所有平台上都支持。那些所有平台上都有的功能通常表现一致，但也可能存在很难修复的细微差异。由于架构差异，Avalonia UI在平台一致性的问题上很容易超越其他框架。Avalonia 完全自己渲染，因此它在每个平台上看起来总是完全相同（字体、输入差异、弹出窗口等除外）。

- 原生控件集成

  .NET MAUI和Uno Platform都建立在Xamarin Native之上，并与之完全集成。这意味着两个框架都可以通过c#绑定访问特定于平台的原生控件。这对于访问原生平台功能和控件来说非常强大，几乎没有任何妥协。可以直接在XAML和代码隐藏中添加原生控件，就像框架本身内置的任何其他控件一样。相比之下，Avalonia UI是它自己的UI层，它不直接与Xamarin Native(及其特定于平台的控件)集成。作为替代，Avalonia提供了一个允许在Avalonia应用程序中嵌入本地控件的NativeControlHost。但是，这并不像 MAUI或者Uno Platform中那样简洁。类似于WPF中的WindowsFormsHost，但与之不同的是，Avalonia UI 还使用 3D 元素解决了“空域问题”，可以直接在各种表面上绘制 UI。这实际上允许Avalonia在游戏引擎或DirectX上运行，这在其他框架中是不可能的。

- XAML 语法和代码共享

  在代码共享方面，Uno Platform拥有最高的评分。它使用与 UWP/WinUI相同的XAML方言和对象模型，这使得它在XAML和C# 100% 兼容。Avalonia和MAUI都偏离了过去的XAML版本，与WPF或UWP/WinUI都不兼容。尽管如此，Avalonia努力在对象模型方面与WPF相似， MAUI会因为很少的原因（Height/Width, TextBlock等）而偏离。在一些情况下，Avalonia还成功地成为了更强大的下一代WPF语法和对象模型。由于对XAML的一些改变（样式，bool类型的IsVisible，简化的网格行/列语法等），使得一些操作在Avalonia中更容易。与MAUI相比，Avalonia与现有WPF 代码的兼容性和代码共享更好，因此总体评分也更高。

- 高级文本格式

  最初的XAML框架WPF具有非常先进的文本格式API（FlowDocument）。这仍然比今天在WinUI 3或之前的UWP中发现的更高级。事实上，在Avalonia UI版本11.0之前，没有其他跨平台XAML框架支持高级文本特性。现在，Avalonia UI具有与WPF几乎相同的API，并且可以完成在 .NET MAUI和Uno Platform上根本不可能完成的文本格式化和测量。由于架构上的差异，在可预见的未来，Avalonia UI很可能仍将是唯一支持高级文本(不依赖第三方控件)的框架。这包括诸如RichTextBox之类的控件，这些控件可以在Avalonia中实现，但在Uno Platform中非常困难，在 .NET MAUI中几乎是不可能的。

- 非UI功能

  Avalonia UI的主要缺点是它只是一个UI框架。.NET MAUI有必备的软件包，Uno Platform是继UWP之后的一个完整的应用开发平台。可以说.NET MAUI和Uno Platform都不仅仅是一个UI框架。这意味着在.NET MAUI和Uno Platform中诸如持久化设置、文件处理、身份验证、本地化和设备权限等内容都可以立即使用，但在 Avalonia不行。Uno Platform甚至具有一些仅在UWP中才能找到的音频相关的高级API，并且可以跨平台。Uno Platform试图覆盖整个UWP的对外暴露的API（API-surface），这包含大量的API。整个API 是自动生成的，其中许多功能未实现stubs。这意味着大多数非 UI的API不可用，如果在应用中使用它们，则会引发异常。这确实会在开发过程中产生一些问题，但编译器会显示正在使用哪些未实现的API。尽管如此，Uno Platform依然比其他框架拥有更多的非UI功能。

- 性能

  XAML源自于桌面应用，本身也相当消耗资源。WPF(最初的XAML框架)通常在运行时从XAML标记中构建整个视图，这在首次加载时可能会严重影响性能。此外，使用MVVM是通过反射绑定把控件绑定到viewmodel上，相比于编译后的代码，反射绑定本来就慢一些。最重要的是，传统的XAML控件具有更高的性能和系统要求，这可能是移动平台或云平台需要考虑的问题。UWP和Uno Platform通过x:Load允许懒加载来改进这一点。它们都支持使用x:Bind进行编译绑定。MAUI的体系结构通过使用原生控件完全避免了第一个问题。Avalonia UI已在很大程度上切换到预编译的XAML和编译绑定，这也解决了这两个问题。这三种框架理论上性能都优于WPF。与性能相关的 MVU 模式不应被忽视。UI 不是由 XAML 标记构造的,它通常是在代码中和代码隐藏中的业务逻辑一起构造。默认情况下，这意味着控件和用户界面元素只有在被代码引用并需要显示时才会构造。通过这种方式，使用MVU模式的性能有望超过MVVM模式应用程序的性能。MAUI和Uno Platform都支持MVU模式。Avalonia也完全支持在代码中创建UI，而不使用XAML，从而获得同样的性能优势。MAUI的性能并非故意评为两颗星，低于Avalonia的三颗星。其原因是：MAUI使用原生控件，是互操作。本机编译在很大程度上缓解了这一问题，但C#和Android控件集成都会降低性能。然而，Avalonia完全渲染自己，并且不与android原生控件交互(除非托管本机视图)。这意味着Avalonia基本上可以拥有视频游戏（video game）的性能。Uno Platform的性能在大多数平台上通常都是足够的。但是，在Android上，.NET运行时和Java运行时之间存在严重的互操作性能问题。这是.NET和Android本身的问题。然而，由于Uno Platform的体系结构(与本机控件集成)，这种互操作总是必需的。这意味着，在Android上，Uno Platform的性能从根本上不如其他框架，并且Android上的高性能Uno Platform应用程序目前是不可能实现的。

- 应用稳定性

  MAUI的移动应用稳定性与Uno Platform排名相同;但是，在不同平台上遇到需要用大量针对特定情况的代码和标记来处理的布局问题是很常见的。Uno Platform还有许多没有处理的情况和一些bug，这些都会在整个开发过程中出现。这在很大程度上是快速开发速度优于正确性和稳定性的策略带来的结果。相比之下，Avalonia UI从一开始就考虑到稳定性:它的功能是完整的。在实践中，Avalonia UI可能是最稳定和最容易开发的。

- 代码的许可协议

  Uno Platform采用的不是MIT license，而是Apache 2.0。Apache license不像MIT那样宽松。除了别的方面，这阻止了代码共享回其他MIT许可的框架。Uno Platform可以使用MIT许可项目（如 WinUI、WPF和Avalonia）的源代码，但这些项目基本上不能使用Uno Platform的代码。这就是为什么Uno Platform在这里排名较低。Avalonia UI最初完全是MIT授权的，并获得了三星评级。但是，随着 [re-licensing of the composition renderer](https://raw.githubusercontent.com/AvaloniaUI/Avalonia/master/src/Avalonia.Base/Rendering/Composition/License.md)，禁止以原始二进制形式以外的任何形式进行修改和分发，这降低了分数。合成渲染器（composition renderer）是 Avalonia版本11+中唯一支持的渲染器，其他渲染器已被删除。这使得修改Avalonia并在您自己的应用程序中分发它被禁止。该团队已经澄清，该许可证将“在v11进入GA时恢复到MIT”。（此部分于2023年7月废弃，有下一段内容替代。） Avalonia UI完全是MIT授权的，可以在大多数.NET基金会和WinUI项目之间免费共享代码。对于需要完全掌控UI框架以达到快速推送修复，确保特定应用稳定性的目的，甚至是想替换自定义的内部组件的公司来说，Avalonia UI是一个理想的选择。

- 支持

  尽管MAUI是由Microsoft开发的，Avalonia和Uno Platform在付费支持方面的排名依旧都高于MAUI。这主要是由于小公司的敏捷性。Microsoft现在高度官僚主义，任何反馈或变化，即使是微小的，都需要广泛的讨论才能采取行动。这与Avalonia形成鲜明对比，Avalonia可以非常快速地采用新功能，Uno Platform沟通速度也非常快。在免费支持方面，Uno Platform社区的响应次数与更为庞大的MAUI社区相当。但是Uno Platform通常可以更快的解决bugs并实现功能。

- 代码库的易读性和易于贡献

  Avalonia UI拥有最干净的代码库，大大降低了公众贡献的门槛。Uno Platform和 .NET MAUI的要复杂得多，可以从代码上看出这点。从长远来看，复杂性的增加通常在维护和稳定性方面成本变得很高。在Uno Platform中，这种复杂性对于满足体系结构目标和支持原生控制集成是必要的。

- 开发体验

  Avalonia UI拥有最好的整体开发体验。代码库易于阅读，使用Rider的开发调试体验是一流的(在其他IDE上则要差一些)。.NET MAUI紧随其后，因为它现在与Visual Studio的集成超过了所有其他的框架。由于需要在每个平台上分别验证/调整每个特性/视图，.NET MAUI在整体开发体验方面存在不足。Uno Platform的开发体验很差，与Visual Studio的集成较少，编译时间长，调试也很困难。有关开发体验的更多详细信息，请参阅IDE集成部分。

- 企业支持

  乍一看，Microsoft开发的 .NET MAUI似乎拥有最强大的企业支持。然而，Microsoft并没有在这个项目上投入大量资源，根据Microsoft放弃UI框架的历史，对MAUI的支持也存在不确定性。Avalonia虽然最初是完全开源的，但现在得到了一些核心团队成员的公司的支持。这为维持项目提供了良好的稳定性和收入衡量标准。但是，需要谨慎的是企业对其影响的增加以及代码库闭源的进展。例如，合成渲染引擎现在不是可以修改的自由许可证（而其余代码是 MIT 许可的），这一点会在V11正式版发布后改回来。Uno Platform凭借创新和令人难以置信的沟通和响应次数，在企业赞助方面依然独树一帜。值得注意的是，它们与Microsoft 有着合作以及密切的沟通。

- Visual Studio集成

  没有一个框架在Visual Studio集成方面有三星。这是因为Visual Studio历来专注于windows平台框架，如WinForms、WPF、UWP和WinUI，并以不可扩展的方式对这些框架进行硬编码支持。但是，.NET MAUI的支持有了很大的改进（从发布时几乎无法使用开始）。Uno Platform的Visual Studio集成还有很多需要改进的地方，显然是三者中开发体验较差的一个。这不是他们的错，因为Microsoft不合理地支持使用 .xaml 文件的任何其他项目类型。Visual Studio中的Avalonia支持提供了可靠的预览器支持，并且大多数功能都可以工作- 通过使用特殊的.axaml扩展名 - 但XAML并不像其他IDE（如Rider）那样流畅。

- Visual Studio Code集成

  Uno Platform团队为[Visual Studio Code开发了一个扩展](https://platform.uno/blog/announcing-net-mobile-debugging-in-vs-code-mobile-development-in-vs-code-with-uno-platform-or-net-maui/)，支持开发，更重要的是，可以调试移动和Web应用程序。这是VS Code工具向前迈出的一大步，而VS Code工具作为C#/.NET应用程序的IDE历来对开发人员不友好。令人惊讶的是，该扩展还支持.NET MAUI应用程序。Uno Platform团队确实在这方面迈出了一步，填补了VS Code支持C#/.NET应用程序方面长期存在的空白，因此Uno Platform在这款IDE集成方面获得了三颗星的评价。Uno Platform应用程序现在在Visual Studio Code中得到了最好的支持(除非在Windows上开发WinUI，其中Visual Studio仍然是最好的)。请注意，这个扩展不是开源的。Avalonia UI 于2023年7月[公布](https://avaloniaui.net/Blog/avalonia-for-visual-studio-code-early-access,e2464208-4482-4dd1-bd60-fd11c98983dc) 了一个支持XAML预览和代码补全的[Visual Studio Code插件预览版](https://github.com/AvaloniaUI/Avalonia-VSCode-Extension)。这使得Avalonia UI在Visual Studio Code中更易于开发，并将使其成为一个可选的IDE。

- 设计工具集成

  目前只有Uno Platform支持设计工具(Figma)来构建UI。这种支持是由一个闭源XAML生成器提供的。过去Microsoft Blend 可供WPF支持相同的作用。生成的XAML的质量和效率可能不足，但是，对于那些在设计与开发团队之间有明确划分的公司来说，它有助于设计师到开发人员的过渡。.NET MAUI 不支持任何设计工具，并且由于其体系结构可能永远不会。然而，它对XAML的实时编辑提供了开箱即用的支持，这使得设计人员可以在添加代码之前直接在应用程序中调整和添加一些UI元素。Uno Platform也支持XAML的实时编辑。

- 平台支持

  Uno Platform支持大多数平台，几乎可以在任何设备上运行，并取得不同程度的成功(它最强大的领域是移动端和网页)。Uno Platform通过WinUI/UWP直接支持Windows桌面应用，因此在Windows桌面原生应用中获得了最高的排名，需要注意的是，在Uno Platform中，某些后端和平台缺少其他后端和平台具有的功能。这可能会导致你可以在iOS/Android上做一些不能在Linux上做的事情。因此，平台支持并不一致，应该仔细审查。在 macOS 上尤其如此，Uno Platform在上次测试（2021 年）时运行极差。如今，使用macCatalyst构建macOS应用通常会更好，因为Uno Platform对iOS的支持明显更好、更完整。Skia后端也适用于所有桌面平台(甚至是旧版本的Windows)。请记住(如性能部分所述)Uno Platform在Android上的性能不如iOS。Avalonia UI远远领先于macOS和Linux桌面平台的其他框架。Avalonia在Windows桌面平台上的得分也很高，但没有使用原生UI工具包，所以得分比Uno Platform低一些。移动端和Web支持是 11.0 版中新发布的，可能需要一些时间才能稳定下来，因此目前得分较低。Avalonia的Web实现呈现为HTML5 canvas。这永远不会像Uno Platforms架构那样好，在Uno Platforms中，它完全集成为HTML元素。.NET MAUI根本不支持Linux或Web。在平台覆盖面上明显不如其他两个框架。 

## 各平台框架推荐

在每个平台上，都有性能最佳的框架。这也是主观的; 但是，总体而言，评估应该是正确的，并考虑到所有的因素。

| 平台     | 最佳框架                                                     |             |
| -------- | ------------------------------------------------------------ | ----------- |
| Windows  | WPF/WinUI                                                    |             |
| macOS    | [![img](https://avaloniaui.net/img/logo/avalonia-white-purple.svg)](https://avaloniaui.net/img/logo/avalonia-white-purple.svg) | Avalonia UI |
| Linux    |                                                              |             |
| Android  |                                                              |             |
| iOS      | [![img](https://uno-website-assets.s3.amazonaws.com/wp-content/uploads/2021/03/24151905/uno-logo-tm.svg)](https://uno-website-assets.s3.amazonaws.com/wp-content/uploads/2021/03/24151905/uno-logo-tm.svg) |             |
| Web/Wasm |                                                              |             |

如果一个应用程序只需要用于桌面平台，Avalonia是一个非常好的选择。它对Windows的支持是一流的，只是因为不是原生UI，所以排在WinUI或WPF之后。然而，Avalonia在桌面应用程序中没有明显的短板，许多桌面应用程序已经在使用它了。事实上，Avalonia甚至支持在WPF中无法完成的操作，例如在DirectX表面上覆盖 XAML控件。

如果应用程序需要跨平台，可以先用WinUI或WPF编写。在Windows上使用WPF代码库可以很好地转换为Avalonia，但仍然需要三种不同的XAML变体。出于这个原因，通常最好使用WinUI，因为它可以与Uno Platform的代码100%共享。这样就只需要两种XAML变体。

另请注意：

- Web/Wasm是Uno Platform的一个明显优势。由于架构差异（完全使用Skia渲染），Avalonia很难在这个方面竞争。
- Avalonia UI更像是Flutter的竞争对手。它使用Skia（或者选用Windows上的Direct2D）在每个平台上完全渲染自己。这比UnoPlatform有很大的性能优势，尤其是在macOS和Android上。通过这种方式，Avalonia拥有所有框架中最纯粹的架构和最低的社区参与门槛。
- Avalonia UI被定位为下一代WPF，它重新实现了大部分功能。Avalonia从WPF(Grid, text formatting)和WinUI (ItemsRepeater, touch input APIs)中汲取思想和代码，同时仍然有一些其他XAML框架中没有的独特想法(在某些方面更接近CSS的高级样式系统)。它现已为桌面应用开发人员准备就绪，尤其是那些已有WPF代码的开发人员。对于UWP/WinUI开发人员来说，这个过渡不太平滑，但在版本11中添加了UWP/WinUI的最新功能以改进过渡。对于不想更改现有WPF代码的企业应用程序，Avalonia还提供了Avalonia XPF，它在Avalonia渲染引擎之上实现了开源的WPF代码库。
- .NET MAUI特意没有列为任何平台最佳方案。它对于没有复杂 UI 的小型应用程序最有用。即便是在中等复杂程度的应用程序中，它的实用性以及在不同平台之间共享代码的能力，很快就要落后于其他的框架。然而，在某些业务线或更简单的应用程序中，MAUI可能是更好的选择。MAUI最近还能够同时托管Blazor和Avalonia UI，这为某些场景提供了一个有趣的选择。
- Windows 10之前的版本最合适的选择是Avalonia。虽然Uno Platform也有依托Skia的解决方案，但它在功能，稳定性和完整性方面远远落后。
- 如上表所示，使用两种XAML方言（dialects of XAML）可以很好地支持所有平台。WinUI/UWP适用于Windows（Uno Platform用于移动端），其余的使用Avalonia。

参考和链接

1. [Question: XAML Flavor, Architecture & Roadmap](https://github.com/dotnet/maui/issues/43)
2. [The New .NET Multi-platform App UI](https://devblogs.microsoft.com/xamarin/the-new-net-multi-platform-app-ui-maui)
3. [Goodbye Xamarin.Forms](https://itnext.io/goodbye-xamarin-forms-f41723fb9fe1)
4. [Putting “Universal” back into UWP](https://medium.com/@Arlodottxt/putting-universal-back-into-uwp-d2e5a8099bc1)
5. [Evolution of Client Development: Richard Campbell](https://youtu.be/nbqe9uHWT_c?t=232)
6. [[Spec\] Slim Renderer Architecture](https://github.com/dotnet/maui/issues/28)
7. [Discussion: Compatibility with WPF, UWP and WinUI](https://github.com/AvaloniaUI/Avalonia/discussions/5596)
8. [Project Reunion: An End to Microsoft’s UI Madness?](https://medium.com/young-coder/project-reunion-an-end-to-microsofts-ui-madness-1af662e36386)

## 结论

我们花了数年时间才走到这一步，但我们终于有了一些涵盖所有用途的强大的 .NET UI框架。有趣的是，这些框架都发展了一些各自特有且几乎互补的功能。您可能想要尝试的所有内容都包含在其中一种方法中。今天，我们可以编写运行良好的跨平台XAML/C# 应用程序。大多数这项技术（除了UI层）都是基于Mono的，所以大部分功劳都归功于Xamarin。

每个框架所取得的成就都是了不起的。然而，没有一个在所有平台上都占主导地位，每个框架都有自己的优势和劣势。Uno Platform源自于Android/iOS，它在移动平台和web端是最强的。Avalonia源自桌面应用程序，在Windows/Linux/macOS上运行效果最好，但移动设备支持上正在迅速发展。截至 2023 年，Uno Platform的macOS支持充其量只是实验性的，只能用于开发简单的应用程序。截至2023年，Avalonia最初仅支持移动设备，但实际上在所有平台上都更加稳定。不过，目前可能还是需要使用两种不同的UI框架实现基于XAML的跨平台UI。

------

原文链接：https://github.com/robloo/PublicDocs/blob/master/XAMLFrameworkComparison.md

作者：czwy

出处：https://www.cnblogs.com/czwy/p/17572226.html

版权：本作品采用「[署名-相同方式共享 4.0 国际](https://creativecommons.org/licenses/by-sa/4.0/)」许可协议进行许可。
