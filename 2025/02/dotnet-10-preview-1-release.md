---
title: .NET 10 Preview 1发布
slug: dotnet-10-preview-1-release
description: 今天.NET 10 Preview 1发布了，我第一时间下载，升级了Avalonia UI项目和博客网站，前者功能测试及AOT发布正常，后者调试正常，Docker暂时未成功
date: 2025-02-25 05:01:14
lastmod: 2025-02-25 06:21:12
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2025/02/cover_06.jpg
categories:
  - 分享
  - .NET
tags:
  - 编程
  - .NET
  - Preview
---

今天，微软发布了 .NET 10 的首个预览版本。作为一名 .NET 开发者，我第一时间下载并进行了测试。让我们一起来看看这个版本带来了哪些更新，以及我的实际测试情况。

## 一、 .NET 10 Preview 1 主要更新

微软在这个版本中为 .NET 生态系统带来了多个方面的增强：

### 1. 运行时和基础库改进
- 新增多个字符串处理和时间相关的 API
- ZipArchive 性能和内存使用优化
- 支持 AVX10.2
- 数组接口方法去虚拟化

### 2. C# 语言特性
- 无绑定泛型中的 nameof 支持
- 隐式 span 转换
- 字段支持的属性
- lambda 参数修饰符支持
- 实验性功能：数据段中的字符串字面量

### 3. ASP.NET Core 与 Blazor
- OpenAPI 3.1 支持
- YAML 格式的 OpenAPI 文档生成
- Blazor 路由属性语法高亮
- QuickGrid 组件增强

### 4. .NET MAUI
- iOS 和 Mac Catalyst 的 CollectionView 增强
- Android 16 (Baklava) Beta 1 支持
- JDK-21 构建支持

## 二、个人测试情况

我在第一时间进行了以下项目的升级测试：

1. **Avalonia UI 项目**
   - 功能测试全部通过
   - AOT 发布测试成功
   - 性能表现正常

参考项目：[CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox)

2. **博客网站**
   - 本地调试运行正常
   - Docker 部署暂时遇到问题，需要进一步调试

参考项目：[CodeWF](https://github.com/dotnet9/CodeWF)

## 三、如何开始使用

如果你也想尝试 .NET 10 Preview 1：
1. 下载并安装 [.NET 10 SDK](https://dotnet.microsoft.com/zh-cn/download/dotnet/10.0)
2. 如果使用 Visual Studio，建议安装最新的 [Visual Studio 2022 预览版]([Visual Studio Preview](https://visualstudio.microsoft.com/zh-hans/vs/preview/#download-preview)，[VS离线安装包制作](https://learn.microsoft.com/zh-cn/visualstudio/install/create-a-network-installation-of-visual-studio?view=vs-2022#download-the-visual-studio-bootstrapper-to-create-the-layout)教程点击
3. VS Code 用户可以安装 C# Dev Kit 扩展

更多详细信息可以查看[官方博客公告](https://devblogs.microsoft.com/dotnet/dotnet-10-preview-1/)。

后续我会持续关注 .NET 10 的开发进展，并分享更多实践经验。

