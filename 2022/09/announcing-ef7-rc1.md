---
title: EF CORE 7 RC1 发布
slug: announcing-ef7-rc1
description: Entity Framework Core 7 (EF7) Release Candidate 1 已发布！该团队专注于解决缺陷、小幅改进以及对功能进行最后润色。
date: 2022-09-15 08:35:17
copyright: Reprinted
author: Jeremy Likness
originalTitle: Announcing Entity Framework 7 Release Candidate 1
originalLink: https://devblogs.microsoft.com/dotnet/announcing-ef7-rc1/
draft: false
cover: https://img1.dotnet9.com/2022/09/ef7.jpeg
categories: .NET
tags: .NET
---

> 原文链接：[https://devblogs.microsoft.com/dotnet/announcing-ef7-rc1/](https://devblogs.microsoft.com/dotnet/announcing-ef7-rc1/)
>
> 原文作者：Jeremy Likness
>
> 翻译：沙漠尽头的狼(谷歌翻译加持)

Entity Framework Core 7 (EF7) Release Candidate 1 已发布！该团队专注于解决缺陷、小幅改进以及对功能进行最后润色。

在 GitHub 上查看[EF7 RC1 更改的完整列表](https://github.com/dotnet/efcore/issues?q=milestone%3A7.0.0-rc1)。

要详细了解 EF7 中的新增功能以及工作示例，请查看我们最新更新的 [EF7 文档中的新增功能](https://docs.microsoft.com/ef/core/what-is-new/ef-core-7.0/whatsnew)。您还可以阅读我们之前的博客文章中的功能深入探讨：

- [EF7 Preview 7 – Interceptors](https://devblogs.microsoft.com/dotnet/announcing-ef7-preview7-entity-framework/)
- [EF7 Preview 6 – Performance](https://devblogs.microsoft.com/dotnet/announcing-ef-core-7-preview6-performance-optimizations/)
- [EF7 Preview 5 – Table-per-Concrete Type (TPC)](https://devblogs.microsoft.com/dotnet/category/dotnet-core/)
- [EF7 Preview 4 – DDD-friendly converters](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-7-preview-4/)
- [EF7 Preview 3 – customizable database-first scaffolding templates](https://devblogs.microsoft.com/dotnet/
- [EF7 Preview 1 – the beginning](https://devblogs.microsoft.com/dotnet/announcing-entity-framework-7-preview-1/)

## EF7 先决条件

- EF7 面向 .NET 6，这意味着它可以在 .NET 6 (LTS) 或 .NET 7 上使用。
- EF7 不会在 .NET Framework 上运行。

EF7 是 EF Core 6.0 的继承者，不要与 [EF6](https://github.com/dotnet/ef6)混淆。如果您正在考虑从 EF6 升级，请阅读我们的[从 EF6 移植到 EF Core 的指南](https://docs.microsoft.com/ef/efcore-and-ef6/porting/)。

## 如何获得 EF7 RC1

EF7 仅作为一组 NuGet 包分发。例如，要将 SQL Server 提供程序添加到您的项目中，您可以通过 dotnet 工具使用以下命令：

```shell
dotnet add package Microsoft.EntityFrameworkCore.SqlServer --version 7.0.0-rc.1.22426.7
```

下表链接到 EF Core 包的 RC1 版本并描述了它们的用途。

| 包裹                                                                                                                                                                   | 目的                                                                                                           |
| :--------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------------------- |
| [Microsoft.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore/7.0.0-rc.1.22426.7)                                                       | 独立于特定数据库提供程序的主 EF Core 包                                                                        |
| [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/7.0.0-rc.1.22426.7)                                   | Microsoft SQL Server 和 SQL Azure 的数据库提供程序                                                             |
| [Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer.NetTopologySuite/7.0.0-rc.1.22426.7) | SQL Server 对空间类型的支持                                                                                    |
| [Microsoft.EntityFrameworkCore.Sqlite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite/7.0.0-rc.1.22426.7)                                         | SQLite 的数据库提供程序，包括数据库引擎的本机二进制文件                                                        |
| [Microsoft.EntityFrameworkCore.Sqlite.Core](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.Core/7.0.0-rc.1.22426.7)                               | SQLite 的数据库提供程序，没有打包的本机二进制文件                                                              |
| [Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Sqlite.NetTopologySuite/7.0.0-rc.1.22426.7)       | SQLite 对空间类型的支持                                                                                        |
| [Microsoft.EntityFrameworkCore.Cosmos](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Cosmos/7.0.0-rc.1.22426.7)                                         | Azure Cosmos DB 的数据库提供程序                                                                               |
| [Microsoft.EntityFrameworkCore.InMemory](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory/7.0.0-rc.1.22426.7)                                     | 内存数据库提供程序                                                                                             |
| [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools/7.0.0-rc.1.22426.7)                                           | 用于 Visual Studio 包管理器控制台的 EF Core PowerShell 命令；使用它来将脚手架和迁移等工具与 Visual Studio 集成 |
| [Microsoft.EntityFrameworkCore.Design](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Design/7.0.0-rc.1.22426.7)                                         | EF Core 工具的共享设计时组件                                                                                   |
| [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/7.0.0-rc.1.22426.7)                                       | 延迟加载和更改跟踪代理                                                                                         |
| [Microsoft.EntityFrameworkCore.Abstractions](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Abstractions/7.0.0-rc.1.22426.7)                             | 解耦 EF Core 抽象；将此用于 EF Core 定义的扩展数据注释等功能                                                   |
| [Microsoft.EntityFrameworkCore.Relational](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Relational/7.0.0-rc.1.22426.7)                                 | 用于关系数据库提供程序的共享 EF Core 组件                                                                      |
| [Microsoft.EntityFrameworkCore.Analyzers](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Analyzers/7.0.0-rc.1.22426.7)                                   | EF Core 的 C# 分析器                                                                                           |

我们还发布了[ADO.NET](https://docs.microsoft.com/dotnet/framework/data/adonet/ado-net-overview)的[Microsoft.Data.Sqlite.Core](https://www.nuget.org/packages/Microsoft.Data.Sqlite.Core/7.0.0-rc.1.22426.7)提供程序的候选版本 1 。

## 安装 EF7 命令行界面 (CLI)

在执行 EF7 Core 迁移或脚手架命令之前，您必须将 CLI 包安装为全局或本地工具。

要全局安装 RC 工具，请使用以下命令安装：

```shell
dotnet tool install --global dotnet-ef --version 7.0.0-rc.1.22426.7
```

如果您已经安装了该工具，则可以使用以下命令对其进行升级：

```shell
dotnet tool update --global dotnet-ef --version 7.0.0-rc.1.22426.7
```

可以将此新版本的 EF7 CLI 用于使用旧版本 EF Core 运行时的项目。

## 每日构建

EF7 候选版本与 .NET 7 候选版本一致。这些版本往往落后于 EF7 的最新工作。考虑使用[每日构建](https://github.com/aspnet/AspNetCore/blob/master/docs/DailyBuilds.md)来获取最新的 EF7 功能和错误修复。

与候选版本一样，每日构建需要 .NET 6。

## .NET 数据社区站会

.NET 数据团队现在每隔一个星期三在太平洋时间上午 10 点、东部时间下午 1 点或 17:00 UTC 进行直播。加入信息流，就您选择的数据相关主题提出问题，包括最新的候选版本。

- [观看我们以前节目的 YouTube 播放列表](https://aka.ms/efstandups)
- [访问 .NET Community Standup](https://live.dot.net/)页面预览即将举行的节目
- [提交](https://github.com/dotnet/efcore/issues/22700)您对嘉宾、产品、演示或其他内容的想法以涵盖

## 文档和反馈

所有 EF Core 文档的起点是[docs.microsoft.com/ef/](https://docs.microsoft.com/ef/)。

请在[dotnet/efcore GitHub](https://github.com/dotnet/efcore) 存储库上提交发现的问题和任何其他反馈。

## 有用的链接

提供以下链接以方便参考和访问。

- [EF Core Community Standup Playlist: https://aka.ms/efstandups](https://aka.ms/efstandups)
- [Main documentation: https://aka.ms/efdocs](https://aka.ms/efdocs)
- [Issues and feature requests for EF Core: https://aka.ms/efcorefeedback](https://aka.ms/efcorefeedback)
- [Entity Framework Roadmap: https://aka.ms/efroadmap](https://aka.ms/efroadmap)
- [Bi-weekly updates: https://github.com/dotnet/efcore/issues/27185](https://github.com/dotnet/efcore/issues/27185)

## 来自团队的感谢

EF 团队非常感谢多年来使用并为 EF 做出贡献的所有人！

欢迎来到 EF7。
