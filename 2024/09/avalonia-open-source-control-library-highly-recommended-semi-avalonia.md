---
title: Avalonia开源控件库强力推荐-Semi.Avalonia
slug: avalonia-open-source-control-library-highly-recommended-semi-avalonia
description: Semi.Avalonia是以MIT协议开源的Avalonia UI框架下的Semi Design主题风格实现，搭配Ursa.Avalonia自定义控件库，为开发者带来全新视觉与功能体验。
date: 2024-09-25 22:26:25
lastmod: 2024-10-14 23:17:26
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2024/09/0101.jpg
categories: 
    - .NET
tags: 
    - C#
    - Avalonia UI
    - Semi Design
    - 主题
    - 自定义控件
---

## Avalonia是什么？

Avalonia是一个强大的框架，使开发人员能够使用.NET创建跨平台应用程序。它使用自己的渲染引擎绘制UI控件，确保在Windows、macOS、Linux、Android、iOS和WebAssembly等不同平台上具有一致的外观和行为。这意味着开发人员可以共享他们的UI代码，并在不同的目标平台上保持统一的外观和感觉。

## MIT 协议普及

MIT 协议（The MIT License）是一种简洁且宽松的开源软件许可协议。它允许使用者自由使用、复制、修改、合并、发布、分发、再许可和 / 或销售软件副本。使用者在软件和软件的所有副本中都必须包含版权声明和许可声明。MIT 协议对使用者的限制很少，基本上赋予了使用者极大的自由，适用于各种开源项目，鼓励代码的共享和重用，促进软件技术的快速发展。

Dotnet和Avalonia都是MIT协议，相关的代码地址是：

- Dotnet：https://github.com/microsoft/dotnet
- Avalonia：https://github.com/AvaloniaUI/Avalonia

##  Semi.Avalonia和Ursa.Avalonia

本文介绍重点来了：一个MIT协议的Avalonia主题库和一个MIT协议的自定义控件库-Semi.Avalonia和Ursa.Avalonia。

[Semi.Avalonia](https://github.com/irihitech/Semi.Avalonia)是以MIT协议开源的Avalonia UI框架下的Semi Design主题风格实现：![](https://img1.dotnet9.com/2024/09/0101.jpg)

仓库地址：https://github.com/irihitech/Semi.Avalonia

搭配同样MIT协议的[Ursa.Avalonia](https://github.com/irihitech/Ursa.Avalonia)自定义控件库，为开发者带来全新视觉与功能体验。

![](https://img1.dotnet9.com/2024/09/0102.png)

仓库地址：https://github.com/irihitech/Ursa.Avalonia

这两个库可放心用于信创及国产操作系统(来源微信公众号【铱泓科技】，文章链接：[点击阅读](https://mp.weixin.qq.com/s/bJhjnXtiG7zNXjFzhZb1Fg))：

> 大熊Ursa和Semi两大Avalonia控件集已经完成与龙芯3A6000和龙架构（LoongArch™）的兼容互认证。这一重要的里程碑标志着我们在推进自主可控和国产化技术方面取得了新的进展。

![](https://img1.dotnet9.com/2024/09/0113.png)

## 控件部分截图

控件都大同小异，简单截几张图：

Semi.Avalonia主题库一览：

![Semi.Avalonia截图](https://img1.dotnet9.com/2024/09/0103.gif)

Ursa.Avalonia自定义控件库一览：

![Ursa.Avalonia](https://img1.dotnet9.com/2024/09/0104.gif)

## 案例

站长公司项目使用了该控件，这里不方便截图，这里放上站长使用Avalonia UI搭配该主题及控件库写的工具：https://github.com/dotnet9/CodeWF.Toolbox

AOT发布后文件：

![](https://img1.dotnet9.com/2024/09/0105.png)

黑白主题：

<table>
    <tr>
    	<td><img src="https://img1.dotnet9.com/2024/09/0106.png" ></td>
    	<td><img src="https://img1.dotnet9.com/2024/09/0107.png" ></td>
    </tr>
</table>

国际化：

![Ursa.Avalonia](https://img1.dotnet9.com/2024/09/0110.gif)

Json美化工具：

![](https://img1.dotnet9.com/2024/09/0108.png)

YAML转Json工具：

![](https://img1.dotnet9.com/2024/09/0109.png)

## 经验分享

1. 控件怎么使用？

很简单，Clone控件仓库，根据Readme及Demo运行效果查找，如我觉得Buton的Warning效果不错：

![](https://img1.dotnet9.com/2024/09/0111.png)

使用VS Code或VS打开

![](https://img1.dotnet9.com/2024/09/0112.png)

1. 展开Semi.Avalonia.Demo
2. 找到Pages目录，打开ButtonDemo.axaml
3. 根据界面关键字Solid、Waring找到需要的样式

OK，本文就介绍到这。