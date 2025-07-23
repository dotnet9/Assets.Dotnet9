---
title: Avalonia自定义标题栏在Win7出现异常
slug: avalonia-win-custom-titlebar
description: Windows 7下自定义Avalonia标题栏出现原生标题栏解决
date: 2025-07-23 22:31:27
lastmod: 2025-07-23 22:56:33
draft: false
cover: https://img1.dotnet9.com/2025/07/0401.gif
categories:
  - Avalonia UI
tags:
  - C#
  - Avalonia
  - TitleBar
  - Windows 7
---

Avalonia 使用自定义标题栏在 Win7 下运行效果：

![](https://img1.dotnet9.com/2025/07/0401.gif)

出现了原生标题栏，怎么解决呢？

只需要 Window 后台代码添加这一行代码：

```csharp
SystemDecorations = SystemDecorations.None;
```

即可正常运行了：

![](https://img1.dotnet9.com/2025/07/0402.gif)
