---
title: Avalonia开源控件库强力推荐-Semi.Avalonia
slug: avalonia-open-source-control-library-highly-recommended-semi-avalonia
description: Semi.Avalonia是以MIT协议开源的Avalonia UI框架下的Semi Design主题风格实现，搭配Ursa.Avalonia自定义控件库，为开发者带来全新视觉与功能体验。
date: 2024-09-25 22:26:25
lastmod: 2024-10-14 23:17:26
copyright: Original
banner: true
draft: false
cover: https://img1.dotnet9.com/2024/09/0101.jpg
albums:
    - Avalonia UI控件库
categories: 
    - Avalonia UI
tags: 
    - C#
    - Avalonia UI
    - Semi Design
    - 主题
    - 自定义控件
---

## Avalonia是什么？

Avalonia是一个强大的框架，使开发人员能够使用.NET创建跨平台应用程序。它使用自己的渲染引擎绘制UI控件，确保在Windows、macOS、Linux、Android、iOS和WebAssembly等不同平台上具有一致的外观和行为。这意味着开发人员可以共享他们的UI代码，并在不同的目标平台上保持统一的外观和感觉。

## MIT 协议的宽松与便利

MIT 协议（The MIT License）是一种简洁且宽松的开源软件许可协议。它允许使用者自由使用、复制、修改、合并、发布、分发、再许可和 / 或销售软件副本。使用者在软件和软件的所有副本中都必须包含版权声明和许可声明。MIT 协议对使用者的限制很少，基本上赋予了使用者极大的自由，适用于各种开源项目，鼓励代码的共享和重用，促进软件技术的快速发展。

Dotnet和Avalonia都是MIT协议，相关的代码地址是：

- Dotnet：https://github.com/microsoft/dotnet
- Avalonia：https://github.com/AvaloniaUI/Avalonia

##  Semi.Avalonia和Ursa.Avalonia

### （一）Semi.Avalonia - 主题风格的魅力实现

Semi.Avalonia，这是以 MIT 协议开源的 Avalonia UI 框架下的 Semi Design 主题风格的精妙呈现。它为应用程序带来独特的视觉风格，如同一幅精美的画卷，为用户界面增添了丰富的色彩和质感。

![](https://img1.dotnet9.com/2024/09/0101.jpg)

其仓库地址为：https://github.com/irihitech/Semi.Avalonia

### （二）Ursa.Avalonia - 自定义控件的创新力量

搭配同样遵循 MIT 协议的Ursa.Avalonia自定义控件库，更是如虎添翼。它们携手为开发者缔造全新的视觉与功能体验，仿佛为开发之旅开启了一扇通往无限可能的大门。

![](https://img1.dotnet9.com/2024/09/0102.png)

仓库地址：https://github.com/irihitech/Ursa.Avalonia

### 在信创及国产操作系统领域表现

值得一提的是，这两个库在信创及国产操作系统领域表现出色，已完成与龙芯 3A6000 和龙架构（LoongArch™）的兼容互认证，这是自主可控和国产化技术推进的重要成果。

下面信息引用来自微信公众号【铱泓科技】8月2号的文章 《[Ursa与Semi正式完成龙架构兼容互认证](https://mp.weixin.qq.com/s/bJhjnXtiG7zNXjFzhZb1Fg)》：

> 大熊Ursa和Semi两大Avalonia控件集已经完成与龙芯3A6000和龙架构（LoongArch™）的兼容互认证。这一重要的里程碑标志着我们在推进自主可控和国产化技术方面取得了新的进展。

![](https://img1.dotnet9.com/2024/09/0113.jpeg)

## 控件部分截图

控件虽各有特色，但都展现出独特的魅力。简单截取几张图，让您一窥其貌：

Semi.Avalonia主题库一览：

![Semi.Avalonia截图](https://img1.dotnet9.com/2024/09/0103.gif)

Ursa.Avalonia自定义控件库一览：

![Ursa.Avalonia](https://img1.dotnet9.com/2024/09/0104.gif)

## 实际案例分享

站长公司项目使用了该控件，虽不便截图展示，但可参考站长使用 Avalonia UI 搭配该主题及控件库编写的工具CodeWF.Toolbox：

仓库：https://github.com/dotnet9/CodeWF.Toolbox

该小工具使用Avalonia+Prism 8模块化开发，AOT 发布后的文件组织结构：

![](https://img1.dotnet9.com/2024/09/0105.png)

其具备黑白主题，营造出不同的视觉氛围：

<table>
    <tr>
    	<td><img src="https://img1.dotnet9.com/2024/09/0106.png" ></td>
    	<td><img src="https://img1.dotnet9.com/2024/09/0107.png" ></td>
    </tr>
</table>

还实现了国际化功能，为全球用户提供便捷体验：

![国际化](https://img1.dotnet9.com/2024/09/0110.gif)

同时，包含实用的 Json 美化工具和 YAML 转 Json 工具，分别如下图所示：

**Json 美化工具**

![](https://img1.dotnet9.com/2024/09/0108.png)

**YAML转Json工具**

![](https://img1.dotnet9.com/2024/09/0109.png)

## 使用经验分享

1. 官方文档

- Semi文档：https://docs.irihi.tech/semi
- Ursa文档：https://docs.irihi.tech/ursa/

2. 源码阅读

首先，克隆控件仓库（上面给出了地址），依据 Readme 及 Demo 运行效果进行查找。例如，若觉得 Button 的 Warning 效果出色：

![](https://img1.dotnet9.com/2024/09/0111.png)

可使用 VS Code 或 VS 打开仓库：

![](https://img1.dotnet9.com/2024/09/0112.png)

1. 展开Semi.Avalonia.Demo
2. 找到Pages目录，打开ButtonDemo.axaml
3. 根据界面关键字Solid、Waring找到需要的样式

如此，便能轻松驾驭这些优秀的控件，为开发工作增添效率与魅力。希望本文能为您在 Avalonia 开源控件库的探索之旅中提供有益的指引和启发，让您在开发道路上创造出更加精彩的应用程序。