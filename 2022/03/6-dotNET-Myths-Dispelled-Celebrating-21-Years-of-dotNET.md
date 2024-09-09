---
title: .NET 很好，你可能对它有一些误解
slug: 6-dotNET-Myths-Dispelled-Celebrating-21-Years-of-dotNET
description: 本文章介绍的是NV显卡训练。CPU训练 仅供参考，部分不同的地方请前往官方网站获取信息。
date: 2022-03-30 11:11:24
copyright: Reprinted
author: 全球技术精选
originalTitle: .NET 很好，你可能对它有一些误解
originalLink: https://mp.weixin.qq.com/s/Ri4RbyNKhb5z7IdUGetvPw
draft: False
cover: https://img1.dotnet9.com/2022/03/cover_29.png
categories: 
    - .NET
tags: 
    - .NET
---

在 20 年前的 2002 年, 微软公布了下一代的软件、服务的愿景和路线，2 月 13 日，Visual Studio .NET 推出，.NET 开发平台的第一个版本正式向世界发布。

到现在为止，.NET 都已经 20 岁了, 它已经成长为一个成熟稳定的平台。

但是，我发现很多开发人员还是对 .NET 有一些偏见和误解，让我们来消除这些误解吧！

如果你身边也有这样的朋友，请把这篇文章转发给他们。

## 误解 1：.NET 只能在 Windows 上运行？

实际上这个说法从早期的 .NET 就一直存在，也确实如此，.NET Framework 最初是为 Windows 构建的，因为包含了很多 Win 32 API 的引用，导致跨平台变得困难。

直到微软在 2016 年认真对待 .NET Core，他们才开始解决 Mono 中的一些问题， 以及对 Win32 API 的挥之不去的依赖。但是在早期，.NET Core 、.NET Framework 、 .NET Standard 这些也让开发人员感到混乱，不过值得庆幸的是，在 .NET 5 和现在的 .NET 6 中，这一切都已成为过去。

如今，.NET 6（最新的 .NET）可以在 Windows、Linux 和 macOS 上运行，并支持 x86、x64、Arm32 和 Arm64。

![](https://img1.dotnet9.com/2022/03/2901.png)

Microsoft 为多个平台提供 SDK 和运行时。

这意味着，您可以在最新的 M1 MacBook 上构建 .NET 应用程序：

![在 2021 MacBook Pro M1 上使用命令行构建一个简单的控制台应用程序。](https://img1.dotnet9.com/2022/03/2902.gif)

## 误解 2：.NET 比 Node/Python/Go/Rust 慢？

实际上，.NET 6 具有极高的吞吐量，并且在 Web 测试中提供的吞吐量是在 Node 和 Python 上运行的任何框架的多倍。

最近几年，.NET 团队非常关注运行时几乎所有方面的核心性能，虽然显然它不会在原始性能上击败 Rust 或 C++，但它在运行 Web 应用方面并不落后。

而 Task Parallel Library 和 Span 为构建吞吐量和性能提供了更高的上限。

根据 TechEmpower Benchmarks 提供的 Web 框架测试报告, 在 Round 15 from February 14, 2018 中，您可以看到 ASP.NET 实际上落后于 Node.js：

![](https://img1.dotnet9.com/2022/03/2903.png)

2018 年：Node.js 第 8 位， ASP.NET Core 13 位 ，Express 在 28 位，Flask 57 位， Django 61 位。

在 Round 20 in February 8, 2021 中，仅仅三年后，.NET 绝对压倒了 Node 和 Python，并且仅次于基于 Rust 的服务。

![](https://img1.dotnet9.com/2022/03/2904.png)

2021 年: .NET Core 在第 8 位, Node.js 56 位, Express 94 位, Flask 111 位, Django 118 位.

在 gRPC 基准测试中，.NET 的表现也非常出色。

![](https://img1.dotnet9.com/2022/03/2905.png)

如果您正在使用 gRPC，请不要考虑 Node 或 Python。

## 误解 3：.NET 过时了？

和 Rust 和 Go 相比，很多人觉得 .NET 是一个过时的平台，实际上，.NET 一直都在更新，并且语法和特性都很先进, 泛型, async/await, 匿名类型, 元组, 模式匹配，Expression 等等。

借助于强大的 LINQ，C# 看起来非常像 JavaScript：

![](https://img1.dotnet9.com/2022/03/2906.png)

根据 GitHub 的 2021 年 Octoverse 状态报告，C# 在过去几年中略有复苏：

![](https://img1.dotnet9.com/2022/03/2907.png)

## 误解 4：开发工具很贵？

实际上，早期的 Visual Studio 开发工具确实很贵！

但是现在，微软不仅提供免费的、功能齐全的 Visual Studio 社区版，你还有其他的选择：

- JetBrains Rider
- 适用于 macOS 的 Visual Studio
- 当然还有 VS Code

最近，我在 MacBook Pro M1 上使用 VS Code 完成了我的大部分 C#/.NET 开发：

![](https://img1.dotnet9.com/2022/03/2908.gif)

## 误解 5：.NET 对开源不友好 ？

早期的 .NET 确实是这样的，但是自从 Satya Nadella 掌权以来，微软在开源方面的整个轨迹已经发生了巨大的转变。不过微软在这方面的转型和成长仍然还有很长的路要走。

.NET 本身由.NET Foundation 管理，.NET 编译器 (Roslyn) 和很多其他内部组件都在 GitHub ，并且自 2015 年以来，它已通过 Red Hat Enterprise Linux 认证。

## 误解 6：.NET 只能开发企业管理系统 ？

实际上，.NET 现在已经发展成一个统一平台，你可以用它开发各种各样的应用，包括桌面软件，Web 服务，3D 游戏等等。

.NET 也有很多构建跨平台应用程序的框架，比如：

- Multi-platform App UI
- Uno Platform
- Avalonia

全文完...

> 原文作者：Charles Chen
>
> 原文标题：6 .NET Myths Dispelled — Celebrating 21 Years of .NET
>
> 原文链接：https://blog.devgenius.io/6-net-myths-dispelled-celebrating-21-years-of-net-652795c2ea27
