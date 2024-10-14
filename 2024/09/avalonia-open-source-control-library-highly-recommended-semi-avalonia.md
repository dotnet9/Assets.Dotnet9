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

##  Semi.Avalonia和Ursa.Avalonia

[Semi.Avalonia](https://github.com/irihitech/Semi.Avalonia)是以MIT协议开源的Avalonia UI框架下的Semi Design主题风格实现：![](https://img1.dotnet9.com/2024/09/0101.jpg)

仓库地址：https://github.com/irihitech/Semi.Avalonia

搭配同样MIT协议的[Ursa.Avalonia](https://github.com/irihitech/Ursa.Avalonia)自定义控件库，为开发者带来全新视觉与功能体验。

![](https://img1.dotnet9.com/2024/09/0102.png)

仓库地址：https://github.com/irihitech/Ursa.Avalonia

## 控件部分截图

控件都大同小异，简单截几张图：

![Semi.Avalonia截图](https://img1.dotnet9.com/2024/09/0103.gif)

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

## 经验简单分享

1. 控件怎么使用？

很简单，Clone控件仓库，根据Readme及Demo运行效果查找，如我觉得Buton的Warning效果不错：

![](https://img1.dotnet9.com/2024/09/0111.png)

使用VS Code或VS打开

![](https://img1.dotnet9.com/2024/09/0112.png)

1. 展开Semi.Avalonia.Demo
2. 找到Pages目录，打开ButtonDemo.axaml
3. 根据界面关键字Solid、Waring找到需要的样式

OK，本文就介绍到这。