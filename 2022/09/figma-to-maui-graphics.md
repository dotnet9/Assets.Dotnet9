---
title: 将 Figma 设计转换为 .NET MAUI Graphics 代码
slug: figma-to-maui-graphics
description: 使用FigmaSharp.Maui.Graphics将Figma设计转换为 .NET MAUI Graphics代码。
date: 2022-09-23 11:42:47
copyright: Reprinted
author: jsuarezruiz
originalTitle: From Figma to .NET MAUI Graphics
originalLink: https://github.com/jsuarezruiz/figma-to-maui-graphics
draft: false
cover: https://img1.dotnet9.com/2022/09/figma-to-maui-graphics-01.png
categories: .NET
tags: Figma,MAUI
---

> 原文链接：[https://github.com/jsuarezruiz/figma-to-maui-graphics](https://github.com/jsuarezruiz/figma-to-maui-graphics)
>
> 原文作者：jsuarezruiz
>
> 翻译：沙漠尽头的狼(谷歌翻译加持)，翻译别扭，建议直接阅读原文

使用**FigmaSharp.Maui.Graphics**将 Figma 设计转换为 .NET MAUI Graphics 代码。基于 MIT 协议，免费开源，关于 MIT 协议给出一张图自行理解。

![](https://img1.dotnet9.com/2022/09/0501.jpg)

**继续介绍：**

两张缩略图看效果：

![](https://img1.dotnet9.com/2022/09/figma-to-maui-graphics-01.png)

![](https://img1.dotnet9.com/2022/09/figma-to-maui-graphics-02.png)

Windows 和 macOS 上可用的工具执行以下步骤：

1. 使用个人访问令牌访问 Figma 文档（Personal Access Token）。
2. 获取所有信息并创建我们可以迭代或操作的节点层次结构。
3. 获取节点后，它会为[.NET MAUI Graphics](https://github.com/dotnet/Microsoft.Maui.Graphics)生成 C# 代码。
4. 生成代码后，它会编译代码以确保没有生成错误。

您可以复制并粘贴代码或将其直接导出到文件中。

**注意：** 这个项目使用并扩展了 [FigmaSharp](https://github.com/microsoft/FigmaSharp)。

请记住，此工具为 .NET MAUI Graphics 生成 C# 代码，而不是使用 .NET MAUI 视图生成 XAML 或 C# 代码。

## 入门

访问[figma.com](https://www.figma.com/)的文档您需要生成**个人访问令牌**(Personal Access Token)。登录 Figma 并在主菜单中，转到**Help and Account** → **Account Settings**并选择**Create new token**。这将是您复制令牌的唯一机会，因此请确保将副本保存在安全的地方。

您有问题、需要支持或想要贡献吗？使用 GitHub [Issues](https://github.com/jsuarezruiz/figma-to-maui-graphics/issues) 反应 bug 和功能请求。

## 已知限制或问题

- 目前，所需的更改依赖于 .NET MAUI Graphics 或 FigmaSharp ，该工具不会生成[矢量](https://github.com/jsuarezruiz/figma-to-maui-graphics/issues/2)或[自定义字体](https://github.com/jsuarezruiz/figma-to-maui-graphics/issues/1)。
- 虽然很快就会修复它，但目前您需要将 Figma 中的根节点设置为位置 0,0。

## 版权和许可

在 MIT 许可下发布的代码。
