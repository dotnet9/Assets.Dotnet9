---
title: .NET Core程序瘦身器发布，压缩程序尺寸到1/3
slug: The-dotNet-core-program-slimmer-is-released-compressing-the-program-size-to-1-to-3
description: .NET Core具有【剪裁未使用的代码】的功能，但是由于它是使用静态分析来实现的，因此它的剪裁效果并不是最优的。
date: 2022-03-10 13:12:32
copyright: Reprinted
author: 杨中科
originalTitle: .NET Core程序瘦身器发布，压缩程序尺寸到1/3
originalLink: https://mp.weixin.qq.com/s/U5mwB9Mxjkw1aRfcdO60uQ
draft: False
cover: https://img1.dotnet9.com/2022/03/cover_08.jpg
categories: .NET
tags: 
    - .NET Core
    - 瘦身
    - 裁剪
---

.NET Core 具有【剪裁未使用的代码】的功能，但是由于它是使用静态分析来实现的，因此它的剪裁效果并不是最优的。它有如下两个缺点：

1. 不支持 Windows Forms 和 WPF，而对于程序剪裁功能需求最强烈的其实反而是桌面程序的开发者。

2. 无法删除运行时没有被使用的程序集。比如，我们的程序中使用了 A 程序集，A 程序又引用了 B、C 两个程序集，A 程序集中只有 M1 方法使用了 B 程序集，而 A 程序集中只有 M2 方法使用了 C 程序集。我们的程序中只调用了 A 中的 M1 方法，而从未调用 A 中的 M2 方法。虽然 C 程序集没有被我们调用过，但是由于【剪裁未使用的代码】功能只是做静态的引用检查，因此 C 程序集仍然不会被剪裁掉。

3. 无法很好地支持反射。由于它是使用静态分析来实现的，因此它可能会剪裁掉运行时才会被通过反射加载的程序集。

因此我开发了一个用来对.NET Core 程序进行瘦身的应用程序，它则可以解决上面提到的.NET Core 的【剪裁未使用的代码】问题，它`支持Windows Forms和WPF` ，它会在运行时分析程序加载的程序集，从而得知哪些程序集没有被使用，因此它不仅 `能删掉更多没有被使用的程序集`，而且能天然地 `支持反射` 。

程序剪裁效果对比：

|                  | 原始尺寸 | .NET 内置剪裁 | Zack.DotNetTrimmer |
| ---------------- | -------- | ------------- | ------------------ |
| 空 Core MVC 项目 | 97MB     | 50.3MB        | 43.6MB             |
| 空 WebAPI 项目   | 93MB     | 46.3MB        | 34.5 MB            |
| 空 WPF 项目      | 152 MB   | 不支持        | 75.2 MB            |
| 空 WinForms 项目 | 152 MB   | 不支持        | 50.0 MB            |

项目开源地址：[https://github.com/yangzhongke/Zack.DotNetTrimmer/](https://github.com/yangzhongke/Zack.DotNetTrimmer/)
