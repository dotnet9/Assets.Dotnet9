---
title: 好消息：NET 9 X86 AOT的突破 - 支持老旧Win7与XP环境
slug: good-news-breakthrough-in-net-9-x86-aot-supports-old-win7-and-xp-environments
description: .NET 9开始，X86 AOT支持Win7和XP，不仅仅只支持SP1版本
date: 2024-07-16 05:24:47
lastmod: 2024-07-16 06:09:47
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2024/07/cover_01.png
categories: .NET
tags: .NET 9,AOT,X86,Win7,XP,SP1
---

![](https://img1.dotnet9.com/2024/07/0701.png)

![](https://img1.dotnet9.com/2024/07/0709.png)

## 引言

随着技术的不断进步，微软的.NET框架在每次迭代中都带来了令人惊喜的新特性。在.NET 9版本中，一个特别引人注目的亮点是X86架构下的AOT（ Ahead-of-Time）编译器的支持扩展，它允许开发人员将应用程序在编译阶段就优化为能够在老旧的Windows系统上运行，包括Windows 7和甚至Windows XP。这不仅提升了性能，也为那些依然依赖这些老平台的企业和个人开发者提供了新的可能性。

本文只在分享网友实践的一个成果，如有更多发现，欢迎投稿。

## Win 7支持

站长还未实践，但根据网友技术交流群分享信息可得，这是真的，下图是网友编译的Avalonia UI跨平台项目在Win 7非SP1环境运行效果截图：

![](https://img1.dotnet9.com/2024/07/0702.png)

左侧是程序运行界面，右侧是操作系统版本。

![](https://img1.dotnet9.com/2024/07/0705.png)

![](https://img1.dotnet9.com/2024/07/0706.png)

Winform都可以 x86 aot运行..

![](https://img1.dotnet9.com/2024/07/0707.png)

Winform工程配置如下：

![](https://img1.dotnet9.com/2024/07/0710.png)

## XP支持（理论）

目前测试可运行控制台程序：

![](https://img1.dotnet9.com/2024/07/0703.png)

网友得出结论：

![](https://img1.dotnet9.com/2024/07/0711.png)

XP需要链接YY-Thunks，参考链接：https://github.com/Chuyu-Team/YY-Thunks

![](https://img1.dotnet9.com/2024/07/0704.png)

大家可关注YY-Thunks这个ISSUE：https://github.com/Chuyu-Team/YY-Thunks/issues/66

![](https://img1.dotnet9.com/2024/07/0708.png)



控制台支持XP的工程配置如下：

![](https://img1.dotnet9.com/2024/07/0712.jpg)

网友心得：

![](https://img1.dotnet9.com/2024/07/0713.png)

## 小知识分享

1. .NET 9 X86 AOT简介

.NET 9的X86 AOT编译器通过静态编译，将.NET应用程序转换为可以直接在目标机器上执行的可执行文件，消除了在运行时的JIT（Just-In-Time）编译所需的时间和资源。这对于对性能要求高且需要支持旧版系统的场景具有显著优势。

2. 支持Win7与XP的背景

尽管Windows 7和XP已经不再是主流操作系统，但它们在某些特定领域，如企业遗留系统、嵌入式设备或者资源受限的环境中仍有广泛应用。.NET 9的AOT编译器的这一扩展，旨在满足这些场景的兼容性和性能需求。

3. 如何实现

- **编译过程优化**：NET 9在AOT编译时，对代码进行了更为细致的优化，使得生成的可执行文件更小，启动速度更快。
- **向下兼容性**：通过精心设计的编译策略，确保了对Win7及XP API的兼容性，使代码能够无缝运行。
- **安全性考量**：虽然支持老旧系统，但.NET 9依然注重安全，提供了一定程度的保护机制以抵御潜在的风险。

4. 实例应用与优势

- **性能提升**：AOT编译后的程序通常比JIT执行的程序更快，尤其对于CPU密集型任务。
- **部署简易**：无需用户安装.NET运行时，简化了部署流程。
- **维护成本降低**：对于依赖老旧系统的企业，避免了频繁升级运行时的困扰。

## 结语

.NET 9的X86 AOT支持无疑拓宽了.NET生态的应用范围，为那些需要在老旧平台上运行高性能应用的开发者提供了强大的工具。随着技术的发展，我们期待未来更多的.NET版本能够进一步打破界限，让编程变得更加灵活和高效。

感谢网友GSD分享的这个好消息，大石头这篇文章《各版本操作系统对.NET支持情况》推荐大家阅读：https://newlifex.com/tech/os_net

## 技术交流

软件开发技术交流添加QQ群：771992300

或扫站长微信(codewf，备注“加群”)加入微信技术交流群：

![](https://img1.dotnet9.com/site/wechatowner.jpg)
