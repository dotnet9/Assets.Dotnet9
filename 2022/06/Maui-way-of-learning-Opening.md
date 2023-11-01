---
title: Maui的学习之路 -- 开篇
slug: Maui-way-of-learning-Opening
description: 什么是.NET Maui
date: 2022-06-23 23:08:32
copyright: Reprinted
author: 轩研 Maui开发者
originaltitle: Maui的学习之路 -- 开篇
originallink: https://mp.weixin.qq.com/s/LkKUrldQb1jEyJN8GhaDqA
draft: False
cover: https://img1.dotnet9.com/2022/06/1701.png
categories: MAUI
---

想了很久我决定发一个`Maui`介绍做为开篇，虽然这是老生常谈的话题，但是不能没有这样的探讨（请容我水一篇）。

## 什么是.NET Maui

.NET Maui是微软的一款基于.Net多平台应用 UI (.NET MAUI)的跨平台框架，使用 C# 和 XAML 创建本机移动和桌面应用, 使用.NET MAUI，可以开发可从单个共享代码库在Android、iOS、macOS和Windows上运行的应用。

.NET Maui脱胎于Xamarin.Forms，如果有Xamarin.Forms的使用经验，那么Maui的使用将变得非常得心应手。使用.NET MAUI，可以使用单个项目创建多平台应用，但如有必要，可以添加特定于平台的源代码和资源。.NET MAUI 的主要目标是在单个代码库中实现尽可能多的应用逻辑和UI布局。

![](https://img1.dotnet9.com/2022/06/1701.png)

## .NET Maui支持的平台

- Android 5.0或更高版本(API 21)
- iOS 10或更高版本(UIKit)
- macOS 10.13或更高版本(Mac Catalyst UIKit)
- Windows 11和Windows 10(1809)或更高版本(WinUI3 WindowsAppSdk)
- Tizen，由三星支持（目前已经集成到工程模板中）
- Linux，由社区支持

## .NET Maui的工作原理

.NET MAUI将Android、iOS、macOS和Windows API统一到单个API中，该API允许一次写入一次运行的任何开发人员体验，同时提供对每个本机平台的各个方面的深入访问。

.NET 6 提供了一系列特定于平台的框架来创建应用：.NET for Android、.NET for iOS、.NET for macOS以及 Windows UI 3 (WinUI 3) 库。这些框架都有权访问同一个.NET 6 基类库(BCL) 。此库将基础平台的详细信息从代码中抽象化。BCL 依赖于.NET 运行时，为代码提供执行环境。

对于Android、iOS和macOS，环境由Mono实现，这是.NET 运行时的实现。在Windows，Win32 提供执行环境。

虽然BCL使在不同平台上运行的应用能够共享常见的业务逻辑，但各种平台具有为应用定义用户界面的不同方式，并且它们提供了不同的模型，用于指定用户界面元素的通信和互操作方式。可以使用适用于Android、iOS、macOS、WinUI 3的.Net单独为每个平台创建UI，但此方法要求为每个单独的设备系列维护代码库。.NET MAUI提供了一个框架，用于为移动和桌面应用构建UI。

下图显示了.NET MAUI应用的体系结构的高级视图：

![](https://img1.dotnet9.com/2022/06/1702.png)

在.NET MAUI应用中，编写主要与.NET MAUI API交互的代码，NET MAUI直接使用本机平台API。此外应用代码还可以根据需要直接使用平台 API。

.NET MAUI应用可以在Window PC或Mac上编写（目前需要使用vs2022 preview），并编译为本机应用包：

- Android使用.NET MAUI编译的应用从C#编译到中间语言(IL)，然后在应用启动时(JIT)编译为本机程序集。
- iOS使用.NET MAUI编译的应用完全原生编译(从C#编译为本机ARM程序集代码的AOT) 。
- macOS使用.NET MAUI编译的应用使用Mac Catalyst，这是Apple提供的一种解决方案，它可将使用UIKit生成的iOS应用引入桌面，并根据需要使用其他AppKit和平台API对其进行扩充。
- Windows使用.NET MAUI生成的应用使用Windows UI 3(WinUI 3)库来创建面向Windows桌面的本机应用。

## .NET Maui的其他应用方式

- 虽然.NET Maui已经提供了对各个平台原生的控件的封装，但是你仍然可以使用Maui提供的自绘引擎绘制符合自己需求的控件（Microsoft.Maui.Graphics）
- 你也可以创建.NET MAUI Blazor应用，来达到和网页一样的使用体验，.NET MAUI Blazor应用还需要更新的平台特定的WebView控件，目前支持平台如下：
 - Android 7.0(API 24)或更高版本（Chrome）
 - iOS 14或更高版本（Safari）
 - Mac Catalyst macOS 11或更高版本（Safari）
 - windows 11、Windows 10(1809)或更高版本（Edge webview2）
 - Tizen（未知）
 - Linux（未知）

## .NET Maui开发需要学习的技术知识：

- 基础：
 - .NET
 - C#
 - Xaml
 - Maui
- 扩展：
 - WinUI3 api以及Windows平台Api（Windows）
 - Android api（Android）（通常不需要，如果你需要调用一些硬件）
 - UIKit，iOS平台api（iOS）（通常不需要，如果你需要调用一些硬件）
 - UIKit，Appkit, MacOS api（Mac）
 - Blazor(不是必须)

## .NET Maui的优缺点

- 优点：
 - 使用C# + .Net开发，上手简单，升级容易，配合宇宙第一IDE工作效率不可同日而语
 - 微软技术基本都存在共性（你可以轻松转战WPF）
 - 大厂保证
 - 在不同的平台使用平台自身控件，保证原生性能
 - 配合Blazor可以实现跟网页端一致体验

- 缺点：
 - 不支持win7，甚至还挑win10的版本
 - 目前虽然正式发布但还是不够稳定
 - 因为是C#所以也许可能不如java系或者前端那么容易找到满足的工作（大厂一般都是java）
 - 微软喜欢砍砍砍
 - 虽然保证原生，但是这也就意味着你需要对不同的平台做相关适配（非自绘）
 - 虽然保证原生，这也意味着你需要学习平台相关知识(控件部分行为也有不同)（当然这是所有跨平台应用都需要学习的）

## 同类跨平台开发框架：

- QT（使用C++，我个人认为是目前真正意义上的跨平台，甚至还支持嵌入式）(自绘)
- Flutter（谷歌的跨平台框架，使用Dart语言）(自绘)
- Uno platform（C# 实现方式类似Maui）
- Avalonia（C# 类WPF）(自绘)
- CPF（C# 国产跨平台UI开发框架，支持龙芯）(自绘)
- Electron （网页技术栈方向）

## 相关学习链接：
- Maui：[.NET 多平台应用 UI 文档 - .NET MAUI | Microsoft Docs](https://docs.microsoft.com/zh-cn/dotnet/maui/)
- C#平台调用：[平台调用 (P/Invoke) | Microsoft Docs](https://docs.microsoft.com/zh-cn/dotnet/standard/native-interop/pinvoke)
- Windows Api: [pinvoke.net: memcpy (msvcrt)](https://www.pinvoke.net/default.aspx/msvcrt/memcpy.html)
- WinUI3: [创建第一个WinUI 3（Windows 应用 SDK）项目 - Windows apps | Microsoft Docs](https://docs.microsoft.com/zh-cn/windows/apps/winui/winui3/create-your-first-winui3-app)
- UIKIT：[UIKit Namespace | Microsoft Docs](https://docs.microsoft.com/zh-cn/dotnet/api/uikit?view=xamarin-ios-sdk-12)
- AppKit: [AppKit Namespace | Microsoft Docs](https://docs.microsoft.com/zh-cn/dotnet/api/appkit?view=xamarin-mac-sdk-14)
- Community ToolKit：[使用 .NET 多平台应用 UI (.NET MAUI) Community Toolkit入门 - .NET Community Toolkit | Microsoft Docs](https://docs.microsoft.com/zh-cn/dotnet/communitytoolkit/maui/get-started)