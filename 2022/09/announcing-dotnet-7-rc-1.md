---
title: .NET 7 RC1 发布
slug: announcing-dotnet-7-rc-1
description: 今天我们宣布 .NET 7 候选版本 1。这是生产中支持的 .NET 7 的两个候选版本 (RC) 中的第一个。
date: 2022-09-15 09:01:26
copyright: Reprinted
author: Jeremy Likness，Angelos Petropoulos，Jon Douglas
originaltitle: Announcing .NET 7 Release Candidate 1
originallink: https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-rc-1/
draft: false
cover: https://img1.dotnet9.com/2022/09/What_s_New_in_Net_7_85586957c5.jpg
categories: .NET
tags: .NET
---

>原文链接：[https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-rc-1/](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-rc-1/)
>
>原文作者：Jeremy Likness，Angelos Petropoulos，Jon Douglas
>
>翻译：沙漠尽头的狼(谷歌翻译加持)

今天我们宣布 .NET 7 候选版本 1。这是生产中支持的 .NET 7 的两个候选版本 (RC) 中的第一个。

您可以下载适用于 Windows、macOS 和 Linux 的[.NET 7 Release Candidate 1](https://dotnet.microsoft.com/download/dotnet/7.0) 。

- [Installers and binaries](https://dotnet.microsoft.com/download/dotnet/7.0)
- [Container images](https://mcr.microsoft.com/catalog?search=dotnet/)
- [Linux packages](https://github.com/dotnet/core/blob/master/release-notes/7.0/)
- [Release notes](https://github.com/dotnet/core/tree/master/release-notes/7.0)
- [Known issues](https://github.com/dotnet/core/blob/main/release-notes/7.0/known-issues.md)
- [GitHub issue tracker](https://github.com/dotnet/core/issues)

.NET 7 Release Candidate 1 已通过 Visual Studio 17.4 Preview 2 测试。如果您想在 Visual Studio 系列产品中试用 .NET 7，我们建议您使用[预览通道版本](https://visualstudio.com/preview)。如果您使用的是 macOS，我们建议使用最新的[Visual Studio 2022 for Mac 预览版](https://visualstudio.microsoft.com/vs/mac/preview/)。

不要忘记[.NET Conf 2022](https://dotnetconf.net/)。在 2022 年 11 月 8 日至 10 日与我们一起庆祝 .NET 7 的发布！

在本博客中，我们将重点介绍 .NET 7 的核心主题，并为您提供深入了解细节的资源。

要更详细地回顾 .NET 7 Release Candidate 1 中包含的所有功能和改进，请查看之前的 .NET 7 Preview 博客文章：

- [Announcing .NET 7 Preview 1](https://devblogs.microsoft.com/dotnet/announcing-net-7-preview-1/)
- [Announcing .NET 7 Preview 2](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-preview-2/)
- [Announcing .NET 7 Preview 3](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-preview-3/)
- [Announcing .NET 7 Preview 4](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-preview-4/)
- [Announcing .NET 7 Preview 5](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-preview-5/)
- [Announcing .NET 7 Preview 6](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-preview-6/)
- [Announcing .NET 7 Preview 7](https://devblogs.microsoft.com/dotnet/announcing-dotnet-7-preview-7/)

## .NET MAUI

.NET 多平台应用程序 UI (MAUI) 将 Android、iOS、macOS 和 Windows API 统一到一个 API 中，因此您可以编写一个在多个平台上本机运行的应用程序。.NET MAUI 使您能够提供由每个平台（Android、iOS、macOS、Windows 和 Tizen）专门设计的最佳应用体验，同时使您能够通过丰富的样式和图形打造一致的品牌体验。开箱即用，每个平台的外观和行为都符合其应有的方式，无需任何额外的小部件或样式。

作为 .NET 7 的一部分，.NET MAUI 提供了一个项目来处理跨设备及其平台的多目标。要了解有关生产力改进、工具和性能增强的更多信息，请查看以下资源：

- [Introducing .NET MAUI – One Codebase, Many Platforms](https://devblogs.microsoft.com/dotnet/introducing-dotnet-maui-one-codebase-many-platforms/)
- [Productivity comes to .NET MAUI in Visual Studio 2022](https://devblogs.microsoft.com/dotnet/dotnet-maui-visualstudio-2022-release/)
- [Performance Improvements in .NET MAUI](https://devblogs.microsoft.com/dotnet/performance-improvements-in-dotnet-maui/)
- [.NET Conf Focus on MAUI – That’s a wrap!](https://devblogs.microsoft.com/dotnet/dotnet-conf-focus-on-maui-recap/)

**注意：** 使用 .NET 7 试用 .NET MAUI 的 Visual Studio 体验将在即将发布的 17.4 Preview 2.1 版本中提供。

## 云原生

云原生是一组最佳实践，用于在云中构建应用程序，以利用弹性、可扩展性、效率和速度。

.NET 是构建云原生应用程序的绝佳选择。要了解有关 .NET 7 中的云原生功能和改进的更多信息，请查看以下资源：

- [Announcing built-in container support for the .NET SDK](https://devblogs.microsoft.com/dotnet/announcing-builtin-container-support-for-the-dotnet-sdk/)
- [Announcing gRPC JSON transcoding for .NET](https://devblogs.microsoft.com/dotnet/announcing-grpc-json-transcoding-for-dotnet/)
- [.NET 7 comes to Azure Functions & Visual Studio 2022](https://devblogs.microsoft.com/dotnet/dotnet-7-comes-to-azure-functions/)

## ARM64

ARM提供了小尺寸、卓越性能和高功率效率。

.NET 可帮助您构建在 ARM 设备上运行的应用程序。有关 .NET 7 在 ARM64 上运行速度的更多信息，请查看以下资源：

- [Arm64 Performance Improvements in .NET 7](https://devblogs.microsoft.com/dotnet/arm64-performance-improvements-in-dotnet-7/)

## 现代化

在现代版本的 .NET 上，您可以利用闪电般的性能和大量新功能来提高开发人员的生活质量。

为了使升级体验尽可能无缝，.NET 升级助手为您提供分步指导体验，通过分析和升级您的项目文件、代码文件和依赖项来现代化您的 .NET 应用程序。

有关 .NET 7 如何帮助您实现应用程序现代化的更多信息，请查看以下资源：

- [Incremental ASP.NET to ASP.NET Core Migration](https://devblogs.microsoft.com/dotnet/incremental-asp-net-to-asp-net-core-migration/)
- [Migrating from ASP.NET to ASP.NET Core in Visual Studio](https://devblogs.microsoft.com/dotnet/introducing-project-migrations-visual-studio-extension/)

## 表现

.NET 很快。.NET 7 是目前最快的 .NET。.NET 7 对反射、堆栈替换 (OSR)、启动时间、本机 AOT、循环优化和许多其他领域进行了超过一千项影响性能的改进。

有关为什么 .NET 7 是目前最快的版本的更多信息，请查看以下资源：

- [Performance Improvements in .NET 7](https://devblogs.microsoft.com/dotnet/performance_improvements_in_net_7/)
- [Regular Expression Improvements in .NET 7](https://devblogs.microsoft.com/dotnet/regular-expression-improvements-in-dotnet-7/)

## Contributor spotlight: Filip Navara

向我们所有的社区成员致以巨大的“谢谢”。我们非常感谢您的周到贡献。我们请贡献者[@filipnavara](https://github.com/filipnavara)分享他的想法。

![filipnavara](https://img1.dotnet9.com/2022/09/filip_navara.jpeg)

用菲利普自己的话说：

我从小就开始玩电脑。在拜访我爷爷的时候，我经常看到他在 BASIC 做他的工作。他正在编写工厂自动化软件，我从他那里继承了我对所有技术事物的热爱。DOS 是当时的标准系统，而 Borland 主导了编程工具。我想了解编程的工作原理并学习它。我固执地拒绝了他的所有建议，不得不自己通过反复试验来学习一切。这很愚蠢，但看到这些小程序变得生动起来很有趣。

渐渐地，我开始用不同的语言编程，探索互联网，然后是开源世界。我最喜欢在编译器、操作系统或系统模拟器等低级软件上进行编码。在高中的业余时间，我为 Wine、ReactOS、QEMU、Binutils 和 MinGW 编译器工具集等项目做出了贡献。

当 .NET Framework 的第一个版本问世时，我立刻被吸引住了。它保证了我熟悉的 Delphi 的简单性，而且 C# 语言学习起来真的很有趣。时机恰到好处，因为我和朋友们开始了一个开发电子邮件客户端应用程序的小项目，我们都同意在 .NET 中构建它。那个应用程序，eM Client，让我在整个大学学习期间都忙得不可开交。直到今天，它仍然是我目前的项目；尽管团队已经壮大，但我的职责已经转移，而且我们有很多非常有才华的程序员来解除我的职责。

.NET 的开源对我们来说是一大福音，让很多事情变得更容易。如今，我可以更多地专注于业余项目，为 .NET 做贡献是自然而然的选择。它使我能够充分利用我的知识，从硬件的低级细节和操作系统内部，到我们的电子邮件应用程序构建的高级框架。

开放代码允许我推动一个项目将 WinForms 框架移植到 macOS（基于 Mono 代码，但在许多地方使用 Cocoa 原生控件）。当 .NET 5 统一计划开始实施时，我开始做出更多贡献。对于我们来说，Xamarin.Mac 和 Mono 等不同平台在我们在 Windows 上使用的 .NET 所支持的功能方面一直落后，这一直是我们的痛点。最初，我开始填补 Mono 基类库中的空白，它已经与 .NET Core 共享了一些代码。我意识到这种追赶游戏可能不是最佳解决方案，因此我开始探索其他选项，例如在 CoreCLR 上运行 Xamarin.Mac。它恰好发生在编写第一个 MonoVM（.NET 5+ 中的 Mono 运行时）提交的前几天。一旦我意识到发生了什么，我就加入了这个计划。所有这些工作都隐藏在 GitHub 上，几个月后在 Build 大会上发布了官方公告。看到进展令人激动，构建了我自己的 Xamarin 运行时构建，该构建运行在这个早期的统一 MonoVM 运行时上，显示了第一个 UI。最终，它甚至启动了我们的电子邮件客户端应用程序。这确实改变了我们的游戏规则。使用旧的 .NET Framework，我们无法在新功能发布时使用它们。新版本的部署需要数年时间才能赶上。现在我处于相反的境地，比其他人跑得更早！

这项关于运行时统一的工作现已成功结束，我们向客户发布了具有最新 .NET 6 的应用程序。但是，.NET 中的许多地方仍然可以改进，我喜欢与 .NET 团队的人一起工作。我尝试为每个版本驱动至少一个次要功能。对于 .NET 6，我专注于让 iOS 加密堆栈正常工作。对于 .NET 7，在网络团队的大力帮助下，我尝试了一个用于处理 Negotiate/Kerberos/NTLM 身份验证的 API。虽然它不是一个非常有吸引力或可见的功能，但它是长期的技术债务。单元和功能测试中缺少代码；ASP.NET 通过反射访问内部，对 NativeAOT 不友好；最重要的是，图书馆作者不得不使用复杂的方法来解决缺乏简单公共 API 的问题。

我真诚地希望在未来做出更多贡献，我很高兴看到其他贡献者找到他们感兴趣的领域，并使整个平台对每个人都更好！

## 支持

.NET 7 不是长期支持 (LTS) 版本，因此它将在发布之日起 18 个月内获得免费支持和补丁。重要的是要注意所有版本 LTS 的质量是否相同。唯一的区别是支撑的长度。有关 .NET 支持政策的更多信息，请参阅[.NET 和 .NET Core 官方支持政策](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。

## 路线图

.NET 版本包括产品、库、运行时和工具，代表了 Microsoft 内外多个团队之间的协作。您可以通过阅读产品路线图了解有关这些领域的更多信息：

- [ASP.NET Core 7 and Blazor Roadmap](https://github.com/dotnet/aspnetcore/issues/39504)
- [EF 7 Roadmap](https://docs.microsoft.com/ef/core/what-is-new/ef-core-7.0/plan)
- [ML.NET](https://github.com/dotnet/machinelearning/blob/main/ROADMAP.md)
- [.NET MAUI](https://github.com/dotnet/maui/wiki/Roadmap)
- [WinForms](https://github.com/dotnet/winforms/blob/main/docs/roadmap.md)
- [WPF](https://github.com/dotnet/wpf/blob/main/roadmap.md)
- [NuGet](https://github.com/NuGet/Home/issues/11571)
- [Roslyn](https://github.com/dotnet/roslyn/blob/main/docs/Language%20Feature%20Status.md)
- [Runtime](https://github.com/dotnet/core/blob/main/roadmap.md)

## 结束

我们[感谢](https://dotnet.microsoft.com/thanks)您对 .NET 的所有支持和贡献。请[尝试 .NET 7 Release Candidate 1](https://dotnet.microsoft.com/download/dotnet/7.0)并告诉我们您的想法！