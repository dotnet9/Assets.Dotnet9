---
title: 如何在 2022 年开发桌面
slug: How-to-develop-desktop-in-2022
description: 恭喜，如果你在 2021 年读到这篇文章，那么你已经穿越到了明年。
date: 2022-05-06 06:39:14
copyright: Reprinted
author: 顾中民
originalTitle: 如何在 2022 年开发桌面
originalLink: https://www.bilibili.com/read/cv13119608/
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_20.jpg
categories: 
    - 前端
tags: 
    - Flutter
---

> 原文网址：[https://www.telerik.com/blogs/how-to-desktop-2022](https://www.telerik.com/blogs/how-to-desktop-2022)

## 看看今天为桌面构建的所有不同方法。明智地选择你的生活伴侣。

![](https://img1.dotnet9.com/2022/05/cover_20.jpg)

恭喜。如果你在 2021 年读到这篇文章，那么你已经穿越到了明年。全球大流行已经过去，世界正在恢复正常。对于 Microsoft 堆栈上的开发人员，.NET 6、.NET MAUI 和相关工具已于去年 11 月推出（哈哈，计划赶不上变化，站长 2022 年 5 月 6 日转载这篇文章，至今 Maui 还没有发布正式版，但相信快了，哈哈）。Apple 已经通过稳定的 XCode 版本巩固了 macOS Monterey 和 iOS 15。如果您今天的任务是启动一个全新的桌面应用程序项目，您会使用什么？

首先，请不要让任何人告诉您桌面应用程序是“遗留”的——环顾四周，您将看到大多数企业工作流程依赖于桌面软件。

现在是 2022 年，开发人员在如何为桌面构建方面有很多选择。根据您与谁交谈，过多的技术和选择要么是解放，要么是令人生畏。归根结底，这可能取决于开发人员的体验以及您正在构建的其他内容，以便更好地共享代码。

让我们探索桌面应用程序的技术堆栈——从三种类型的开发人员的角度来考虑。

## 1. 传统主义者

专为桌面而设计，使用原生工具，针对一个平台。

![](https://img1.dotnet9.com/2022/05/2001.jpg)

### 1.1 WinForms

[Windows Forms](https://docs.microsoft.com/en-us/dotnet/desktop/winforms/overview/?view=netdesktop-6.0)于 2002 年随 .NET 1.0 一起推出，是最古老的 Windows 桌面技术之一——令人惊讶的是，它仍然很强大。WinForms 成功的部分原因在于工具的简单性、开发人员的高生产力和丰富的工具生态系统。Windows 上的现代 WinForms 应用程序可以在最新的 .NET 运行时上运行，具有强大的 UI 和对最新 Windows API 的完全访问权限。

### 1.2 WPF

[Windows Presentation Foundation](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/overview/?view=netdesktop-6.0)于 2006 年与 .NET 3.0 一起作为 Windows 桌面技术推出，并且还引入了用可扩展应用程序标记语言 (XAML) 定义的可与业务逻辑分离的 UI 堆栈。毫不奇怪，WPF 在最新的 .NET 运行时上运行并且仍然很强大，同时拥有丰富的开发人员生态系统。现代 WPF 应用程序具有深入的系统集成和最新的触摸/墨迹支持，并散发出现代 UX。尽管有一点学习曲线，但 WPF 使开发人员能够提高工作效率并提供一盘丰富的工具。

### 1.3 UWP

随着 .NET C#/VB/F# 等现代语言的发展，将 .NET 与 XAML 结合用于 UI 标记的趋势在 Windows 8 和 Windows 10 中继续存在。现代需要各种 Windows 设备外形——平板电脑、计算机、2 合一、HoloLens、Surface Hub、Xbox、物联网等。[Universal Windows Platform](https://docs.microsoft.com/en-us/windows/uwp/get-started/universal-application-platform-guide)承诺将开发人员体验结合起来，为所有具有自适应 UI 的 Windows 设备构建，并通过 Microsoft Store 提供应用程序。

诸如桌面桥之类的后续开发允许开发人员将其较旧的 Win32 应用程序打包在 UWP 容器中以发布到应用商店，并能够通过 XAML 岛将现代 UWP UI 嵌入到其他 Windows 桌面应用程序中。虽然最初的 UWP 愿景可能已经发展，但本机运行时和分离的 API 访问层的基础今天很好地为 Windows 应用程序服务。

### 1.4 AppKit/Cocoa

想要为 macOS 构建原生桌面应用程序吗？[AppKit](https://developer.apple.com/documentation/appkit)是你的朋友——定义你需要的所有对象来实现制作 macOS 应用程序的 UI 组件以及在屏幕上有效绘制的细节。AppKit 已融入 Cocoa（macOS 的 Objective-C API 框架）中，但 AppKit 也可以从 SwiftUI 中使用。借助 XCode 中的现代工具，为 macOS 桌面构建只是 iOS 和其他 Apple 平台之外的另一个目标。

## 2. 改良主义者

面向未来的桌面构建, 了解跨平台需求，想要代码共享。

![](https://img1.dotnet9.com/2022/05/2002.jpg)

### 2.1 WinUI 3

[Windows UI Library](https://docs.microsoft.com/en-us/windows/apps/winui/)（WinUI）是用于 Windows 桌面和应用程序 UWP 土生土长的用户体验（UX）的框架。WinUI 3 是下一代原生 Windows UI 堆栈，它与 Windows 10 分离，并将 Fluent Design System 融入所有体验中，以实现一致、直观和可访问的 UX。WinUI 3 为开发人员提供了对桌面和 UWP 应用程序的最新工具和支持。Windows 应用 SDK 提供了一组统一的 API 和工具，可供任何桌面应用以一致的方式使用，适用于广泛的目标 Windows 10 和更高版本的操作系统版本。

### 2.2 Mac Catalyst

事实证明，大多数开发人员都希望为 iOS 构建，将原生 macOS 开发降级以迎合小众受众。Apple 希望通过 Mac Catalyst 解决这个问题——让 iOS，尤其是 iPad 应用程序，能够在 macOS 上无缝运行。Mac 和 iPad 应用程序现在可以与 XCode 共享相同的项目/源代码。AppKit 和 UIkit 之间存在无缝映射，视觉界面针对 iOS 上的触摸和 macOS 上的键盘/鼠标进行了优化。

### 2.3 Flutter

Flutter 在跨平台移动开发中越来越受欢迎——在 Dart 中编写代码并使用小部件在屏幕上绘制像素。Flutter 现在可用于制作[面向桌面的跨平台应用程序](https://flutter.dev/desktop)——Flutter 引擎可以为 Windows、Linux 或 macOS 呈现 UI。虽然对 Flutter 的桌面支持仍处于测试阶段（2022 年 5 月 6 日这天，Flutter 的桌面版本已经正式发布几个月了，可放心使用），但开发人员可以从单个代码库针对 Windows UWP、macOS 和 Linux，并能够为现有 Flutter 应用程序添加桌面支持。

### 2.4 .NET MAUI 桌面

对于 .NET 开发人员，Xamarin.Forms 长期以来一直是制作跨平台应用程序的最简单方法之一，但 Xamarin.Forms 主要迎合 iOS/Android 移动平台。虽然有面向桌面的 Xamarin.Forms 呈现器，但开发人员对面向桌面的信心不大。随着 Xamarin.Forms 向[.NET MAUI](https://docs.microsoft.com/en-us/dotnet/maui/what-is-maui)的演变，这种情况发生了变化。

除了 iOS/Android，.NET MAUI 还完全支持桌面平台——Windows 和 macOS。激发开发人员对 .NET MAUI 信心的原因在于，桌面平台支持是一流的，具有来自单个 VS 项目的精美工具和共享应用程序架构。.NET MAUI 没有重新发明轮子，而是选择通过两种广为接受的技术——WinUI 3 for Windows 和 Mac Catalyst for macOS 在桌面上呈现原生 UI。

### 2.5 Uno 平台

对于真正想要使用“Windows”XAML 标记进行跨平台的 .NET 开发人员，[Uno Platform](https://platform.uno/)提供了一个不错的开源替代方案。为了让 WinUI 无处不在，Uno 平台可以为 Windows、macOS 和 Linux 上的桌面应用程序提供支持——所有这些都来自 C# 和 XAML 的单一代码库和首选 IDE。

### 2.6 Avalonia

被吹捧为 .NET XAML 框架的[Avalonia](http://avaloniaui.net/)提供了另一个开源选项，其中包含基于 XAML 的渲染器，可以为桌面平台（Windows、macOS 和 Linux）上的应用程序提供支持。

## 3. 赶时髦的人

使用非桌面技术创建桌面应用，认为 web 无处不在。

![](https://img1.dotnet9.com/2022/05/2003.jpg)

### 3.1 PWAs

如果使用任何一个 web SPA 框架来构建 web 应用程序，那么[PWA](https://docs.microsoft.com/en-us/microsoft-edge/progressive-web-apps-chromium/) Progressive web apps 是让相同的应用程序在桌面上运行的最容易实现的成果之一。PWA 提供对开放式 web 技术的访问，以实现跨平台的互操作性，并为用户提供为其移动或桌面设备定制的类似应用程序的体验。PWA 本质上是一种网站，经过逐步增强，其功能与支持平台上安装的应用程序类似，通常具有脱机工作、支持推送通知和硬件访问等桌面功能。

从常规的 web 应用开始，开发人员可以选择让他们的应用渐进式发展，添加设备功能的应用程序清单文件和后台线程操作的 JavaScript 服务人员。像[PWA Builder](https://www.pwabuilder.com/)这样的解决方案使这种转换易于开始，开发人员可以深入进行 Windows/Mac 桌面集成。

### 3.2 Electron

希望看到在桌面上运行的 web 应用程序并不是什么新鲜事。进入满足此类需求的最普遍的解决方案——Electron。[ElectronJS](https://www.electronjs.org/)是一个开源项目，用于使用 Web 技术构建跨平台应用程序，并且可以针对任何桌面——Windows、Mac OS 和 Linux。

许多最常用的桌面应用程序本质上都是封装在 Electron shell 中的 Web 应用程序，例如 Visual Studio Code、Microsoft Teams、Slack 和 Figma。

Electron 已经存在了一段时间，并从强大的开发者社区/生态系统中获得了信誉。大多数使用 JavaScript SPA 框架或 .NET/Blazor 编写的现代 Web 应用程序都可以使用 Electron 为桌面解决方案提供支持。Electron 作为托管 Web 应用程序的外壳的优势的核心是稳定和可预测的环境的想法。

为此，每个 Electron 应用程序都捆绑了两个提供可靠性的东西——它自己的 Chromium 引擎副本，用于呈现一致性和 Node.js 运行时。虽然开发人员应该注意他们的应用程序大小/内存占用，但 Electron 已经过实战测试，并为开发人员提供了广泛的 API 以进行深度原生桌面集成。

### 3.3 Blazor Hybrid

如果假设今天有一个稳定的计算环境和现代浏览器的存在，那么 Web 应用程序也许可以通过轻量级 WebView 托管在桌面上——从而创建更小的占用空间并更容易引导应用程序。

借助 Blazor，开发人员可以使用 C#/.NET 构建现代 Web 应用程序，在服务器上运行或通过 WebAssembly 完全在客户端运行。Blazor 非常受现代 .NET 开发人员的欢迎，希望看到 Blazor 强大的桌面解决方案是很自然的。

借助 .NET 6，.NET MAUI 可以提供完美的引导，使[Blazor 混合应用程序](https://channel9.msdn.com/Events/dotnetConf/Focus-on-Windows/Building-NET-Hybrid-Apps-with-Blazor)能够通过现代 WebView（Windows 上的 WebView 2 和 macOS 上的 WKWebView）在桌面上运行。Blazor 可用于构建真正的本机或混合跨平台应用程序，从而邀请 .NET Web 开发人员进入桌面领域。Blazor 组件模型、Razor 渲染引擎、CSS 样式和可扩展性的熟悉性现在可以应用于构建桌面应用程序，同时与 Web 共享代码。

## 美好的未来

无论喜欢与否，桌面都将继续存在。桌面应用程序将继续为许多企业工作流程提供动力，开发人员将不得不为 Windows/MacOS/Linux 桌面构建解决方案。好消息是关于如何到达桌面有很多选择——传统的桌面技术与 Web 或跨平台解决方案愉快地共存，从而实现更多的代码共享。就像一个好父母一样，台式机不会判断——随便你来。好面包就是好面包——不管你怎么上菜。
