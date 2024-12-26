---
title: .NET 程序员的 Playground ：LINQPad
slug: playground-for-dotnet_programmers_linqpad
description: LINQPad 的软件包很小只有二十兆左右，启动速度很快。使用时只需输入想要执行的 C# 语句，并按下 F5 即可
date: 2024-12-26 21:33:59
lastmod: 2024-12-26 21:42:30
copyright: Reprinted
banner: false
author: 码农很忙
originalTitle: .NET 程序员的 Playground ：LINQPad
originalLink: https://www.coderbusy.com/archives/432.html
draft: false
cover: https://img1.dotnet9.com/2024/12/0313.gif
categories:
  - .NET
tags:
  - .NET
  - LINQPad
---

如果想执行一个简单的 C# 语句并获得运行结果，通常我们需要做几个步骤才能达成：

- 打开 Visual Studio 并新建一个控制台项目。
- 在 Program.cs 中编写代码并保存。
- 点击运行按钮或者 F5 运行程序并查看结果。

通常来说这并不会产生问题。但如果你和笔者一样为 Visual Studio 安装了各种插件，那么 Visual Studio 的启动时间就会变得很长。在新建项目时，我们必须为这些临时的代码指定名称和保存路径，如果保持默认的名字，就很可能在今后忘记建立这些文件的用途。

使用 LINQPad 可以解决上面的问题。LINQPad 的软件包很小只有二十兆左右，启动速度很快。使用时只需输入想要执行的 C# 语句，并按下 F5 即可：

![](https://img1.dotnet9.com/2024/12/0301.png)

快捷键 F4 可以打开“查询属性”窗口，在这个窗口中，你可以引用所有在运行时需要的东西，包括：dll、配置文件、json和文本文件等，这些引用的文件将会被复制到输出目录。

![](https://img1.dotnet9.com/2024/12/0302.png)

同时，LINQPad 也支持直接将 NuGet 包引用到查询中：

![](https://img1.dotnet9.com/2024/12/0303.png)

也可以将查询保存为一个扩展名为 .linq 的文件，以便复用代码。

## 语言支持

包括“C# 表达式（C# Expression）”在内，LINQPad 一共支持 4 种语言和 10 种查询类型：

- C# Expression
- C# Statement(s)
- C# Program
- VB Expression
- VB Statement(s)
- VB Program
- SQL
- ESQL
- F# Expression
- F# Program

LINQPad 会根据我们键入的代码自动选择正确的查询类型，大部分时候我们无需担心。

## 结果输出

使用 `Console.WriteLine` 等方法输出的控制台内容会直接在 Result 标签页显示：

![](https://img1.dotnet9.com/2024/12/0304.png)

LINQPad 内置了名为 `Dump` 的扩展方法用于将对象的值展示出来。该方法对 Object 类型进行了扩展，并提供了多个重载，让我们可以对展示结果进行标记：

![](https://img1.dotnet9.com/2024/12/0305.png)

除了简单类型，`Dump` 方法对复杂类型的支持也值得称赞。我们完全可以仅依赖 `Dump` 方法就能了解到某个对象的全部取值：

![](https://img1.dotnet9.com/2024/12/0306.png)

甚至可以直接将一个 WinForm 或 WPF 控件 `Dump` 出来，且支持交互：

![](https://img1.dotnet9.com/2024/12/0307.png)

查询结果也可以进行导出，目前支持：Word、Excel 和 HTML 三种格式。

查询编辑器的左下方是一个状态指示，在这里会展示出查询的运行状态和执行时间。这样，当我们需要简略测试一个算法的效率时，无需再编写额外的监测代码。

## 数据库集成

LINQPad 可以通过 Entity Framework 或者 Entity framework Core 及对应的数据库驱动链接至数据库，比如常见的 SQL Server , MySQL , Oracle 甚至 SQLite 。可以通过程序右上角的“Add connection”完成链接工作：

![](https://img1.dotnet9.com/2024/12/0308.png)

在配置好数据库链接后，我们就可以选定这个链接，编写 C# 代码来访问数据库：

![](https://img1.dotnet9.com/2024/12/0309.png)

除了可以通过执行 `Dump` 方法看到运行结果以外，也可以切换至 `SQL` 标签页查看执行的 SQL 语句：

![](https://img1.dotnet9.com/2024/12/0310.png)

如果需要直接在 LINQPad 中执行 SQL 语句，只需将语言（Language）设置为 SQL 即可：

![](https://img1.dotnet9.com/2024/12/0311.png)

## 图表支持

除了将结果集以表格的形式呈现，LINQPad 也支持直接根据结果集生成统计图。柱状图、折线图、饼状图等均不再话下，且无需很多的额外代码：

![](https://img1.dotnet9.com/2024/12/0312.png)

## LINQPad 的 Visual Studio 扩展 LINQBridgeVs 

LINQBridgeVs 把 LINQPad 强大的 Dump 能力链接到了 Visual Studio 上，支持 2012 到 2019 版本：

![](https://img1.dotnet9.com/2024/12/0313.gif)

## 了解更多

本文涵盖了 LINQPad 的大部分常用操作。作为一个开发者工具，LINQPad 的上手难度并不大。你可以在 https://www.linqpad.net/Resources.aspx 上找到更多关于 LINQPad 的资源。

LINQPad 本身也携带了大量的示例代码，切换左下角的选项卡到 “ Samples ”标签即可看到：

![](https://img1.dotnet9.com/2024/12/0314.png)

## 总结

经过几个月的使用，LINQPad 确实成为了笔者工作中不可或缺的工具。现在，LINQPad 已经被固定在了任务栏，除了运行一些测试性的代码，它也被用来作为数据导出工具和工具箱。笔者最喜欢的是其内置的图表生成功能，当枯燥的数据以图表的形式展示出来时，除了惊艳，就是说不出来的满足。

LINQPad 的销售策略是买断制，一次购买终身有效且可以在最多三台电脑上同时安装，高级版单用户的售价为 700 元人民币左右，同时支持 LINQPad 5 和 LINQPad 6 两个版本。如果确实帮助了你，且经济实力允许，那么购买一个正版授权也未尝不可。
