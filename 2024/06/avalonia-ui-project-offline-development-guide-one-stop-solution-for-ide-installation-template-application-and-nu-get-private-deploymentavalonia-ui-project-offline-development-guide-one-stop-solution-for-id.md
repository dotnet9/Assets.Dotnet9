---
title: AvaloniaUI项目离线开发全攻略：IDE安装、模板应用与NuGet私有化部署一站式解决
slug: avalonia-ui-project-offline-development-guide-one-stop-solution-for-ide-installation-template-application-and-nu-get-private-deployment
description: 本文将指导您如何在本地网络环境中成功安装并配置AvaloniaUI所需的工具和模板。
date: 2024-06-27 21:13:32
lastmod: 2024-06-27 23:29:47
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2024/06/cover_02.png
categories: .NET
tags: .NET,NuGet,AvaloniaUI
---

## 1. 引言

在开始AvaloniaUI项目的离线开发之前，确保您已准备好合适的集成开发环境（IDE）。本文将指导您如何在本地网络环境中成功安装并配置AvaloniaUI所需的工具和模板。

## 2. IDE安装指南

本节部分参考官方文档[《设置编辑器 | Avalonia Docs (avaloniaui.net)》](https://docs.avaloniaui.net/zh-Hans/docs/get-started/set-up-an-editor)，可先了解更多信息。

### 2.1 Visual Studio 2022安装

由于我个人习惯使用Visual Studio开发，因此首先介绍如何在Visual Studio 2022中安装AvaloniaUI扩展。

- 在线制作Visual Studio 2022的离线安装包，请参考文章[《VS2022离线安装包》](https://www.cnblogs.com/sailJs/p/16864697.html)，制作好后再传至内网。
- 下载并安装Visual Studio 2022后，通过链接[Avalonia for Visual Studio 2022 - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AvaloniaTeam.AvaloniaVS)获取Avalonia for Visual Studio 2022扩展，并按照提示进行安装。

![](https://img1.dotnet9.com/2024/06/0201.jpg)

- 安装过程中遇到失败

![](https://img1.dotnet9.com/2024/06/0202.png)

别担心！根据微信群内@rabbitism的解答（感谢@daidai_cn的帮助），我们可以通过解压该扩展文件，删除下图框选的`Extension.vsext`文件后再进行安装：

![](https://img1.dotnet9.com/2024/06/0203.png)

正常安装了：

![](https://img1.dotnet9.com/2024/06/0204.png)

### 2.2. JetBrains Rider安装

官方比较推荐Rider：[JetBrains Rider](https://www.jetbrains.com/rider/) IDE在2020.3版本中开始内置支持Avalonia XAML，包括对Avalonia特定XAML功能和自定义代码检查的一流支持。

离线安装包下载地址：[下载 Rider：跨平台 .NET IDE (jetbrains.com)](https://www.jetbrains.com/zh-cn/rider/download/#section=windows)

## 3. 安装Avalonia UI模板

在线安装请参考文档 [Avalonia Docs](https://docs.avaloniaui.net/zh-Hans/docs/get-started/install)，离线安装请点击 [Avalonia.Templates](https://www.nuget.org/packages/Avalonia.Templates) 下载：

![](https://img1.dotnet9.com/2024/06/0205.png)

安装方式同上图`.NET CLI`命令脚本：

```shell
dotnet new install avalonia.templates.11.0.10.1.nupkg
```

![](https://img1.dotnet9.com/2024/06/0206.png)

现在，无论是在Visual Studio还是JetBrains Rider中，您都可以使用Avalonia UI模板来创建新项目了。

VS 2022中Avalonia UI模板：

![](https://img1.dotnet9.com/2024/06/0207.png)

Rider中使用模板：

![](https://img1.dotnet9.com/2024/06/0208.png)

## 4. 私有化部署NuGet服务

创建好项目后，程序也是无法正常运行的，默认模板依赖Avalonia UI的一些NuGet包，需要在线安装，可以直接把相关库拷贝到内网，但一个一个拷贝、引用还是很麻烦。

为了方便团队内部成员之间共享和管理NuGet包，您可以考虑部署私有NuGet服务。本文推荐使用BaGet作为轻量级的NuGet服务器，参考该[BaGet项目说明](https://github.com/loic-sharma/BaGet)：

1. 安装 [.NET Core 3.1 ](https://dotnet.microsoft.com/zh-cn/download/dotnet/3.1) SDK，该程序支持的.NET最新版（是比较旧了，最近一次更新是2年前），有兴趣可以Clone修改成`.NET 8\9`；
2. 下载最新版的Release压缩包 [Releases · loic-sharma/BaGet ](https://github.com/loic-sharma/BaGet/releases)
3. 运行服务`dotnet BaGet.dll`
4. 浏览器打开`http://localhost:5000`访问：

![](https://img1.dotnet9.com/2024/06/0209.png)

OK，这就算部署完成了，复制图中的标红的URL地址：`http://localhost:5000/v3/index.json`, 在VS中配置NuGet搜索地址吧：

![](https://img1.dotnet9.com/2024/06/0210.png)

## 5. 总结

本文介绍了如何在本地网络环境中成功安装并配置AvaloniaUI所需的开发工具和模板，以及如何部署私有NuGet服务以便团队内部成员之间共享和管理NuGet包。希望这些信息能对您的AvaloniaUI项目开发有所帮助。至于NuGet包的制作、上传需要您从其他途径学习（比如百度），如有其他问题，欢迎随时向我提问。
