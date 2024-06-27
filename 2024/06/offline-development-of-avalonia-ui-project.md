---
title: 离线开发AvaloniaUI项目
slug: offline-development-of-avalonia-ui-project
description: 局域网开发AvaloniaUI项目
date: 2024-06-27 21:13:32
lastmod: 2024-06-27 23:29:47
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2024/06/cover_02.png
categories: .NET
tags: 
---

## 1. IDE安装

参考官方文档 [设置编辑器 | Avalonia Docs (avaloniaui.net)](https://docs.avaloniaui.net/zh-Hans/docs/get-started/set-up-an-editor)

### 1.1. VS

个人习惯VS开发，首先参考文章[《VS2022离线安装包》](https://www.cnblogs.com/sailJs/p/16864697.html)先在线制作离线安装包，制作好后再传至内网。

安装好VS 2022后，下载 [Avalonia for Visual Studio 2022 - Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=AvaloniaTeam.AvaloniaVS) 扩展，然后双击安装...

![](https://img1.dotnet9.com/2024/06/0201.jpg)

等等，安装失败了：

![](https://img1.dotnet9.com/2024/06/0202.png)

在微信群得到兔佬(@rabbitism)解答（最后还是要感谢@daidai_cn），使用压缩软件打开该扩展：

![](https://img1.dotnet9.com/2024/06/0203.png)

删除图中框选文件后再双击安装，可以继续安装了：

![](https://img1.dotnet9.com/2024/06/0204.png)

### 1.2. Rider

看起来官方比较推荐：[JetBrains Rider](https://www.jetbrains.com/rider/) IDE在2020.3版本中开始内置支持Avalonia XAML，包括对Avalonia特定XAML功能和自定义代码检查的一流支持。

下载地址：[下载 Rider：跨平台 .NET IDE (jetbrains.com)](https://www.jetbrains.com/zh-cn/rider/download/#section=windows)

## 2. 安装Avalonia UI模板

参考文档：[安装 | Avalonia Docs (avaloniaui.net)](https://docs.avaloniaui.net/zh-Hans/docs/get-started/install)

离线安装先从 [NuGet Gallery | Avalonia.Templates 11.0.10.1](https://www.nuget.org/packages/Avalonia.Templates) 下载：

![](https://img1.dotnet9.com/2024/06/0205.png)

安装方式一样，使用如下命令：

```shell
dotnet new install avalonia.templates.11.0.10.1.nupkg
```

![](https://img1.dotnet9.com/2024/06/0206.png)

VS 2022可以使用Avalonia UI模板创建项目了：

![](https://img1.dotnet9.com/2024/06/0207.png)

Rider也可以使用：

![](https://img1.dotnet9.com/2024/06/0208.png)

## 3. 私有化部署NuGet服务

创建好项目后，程序也是无法正常运行的，默认模板依赖Avalonia UI的一些NuGet包，需要在线安装，可以直接把相关库拷贝到内网，但一个一个拷贝、引用还是很麻烦，这里建议部署私有化NuGet服务。

这里推荐 [loic-sharma/BaGet: A lightweight NuGet and symbol server (github.com)](https://github.com/loic-sharma/BaGet) 部署，参考该项目说明：

1. 安装 [.NET Core 3.1 ](https://dotnet.microsoft.com/zh-cn/download/dotnet/3.1) SDK，该程序支持的最新版（是比较旧了，最近一次更新是2年前）；
2. 下载最新版的Release压缩包 [Releases · loic-sharma/BaGet ](https://github.com/loic-sharma/BaGet/releases)
3. 运行服务`dotnet BaGet.dll`
4. 浏览器打开`http://localhost:5000`访问：

![](https://img1.dotnet9.com/2024/06/0209.png)

OK，这就算部署完成了，复制图中的URL地址：`http://localhost:5000/v3/index.json`,在VS中配置NuGet搜索地址吧：

![](https://img1.dotnet9.com/2024/06/0210.png)

## 4. 完了

本文就到这，怎么制作NuGet包（第三方包直接从NuGet服务下载，然后上传）？怎么上传？百度一切都有。
