---
title: .NET MAUI开源免费的UI工具包 - Uranium
slug: dot-net-maui-open-source-free-ui-toolkit-uranium
description: "一直有小伙伴在微信公众号后台留言让我分享一下.NET MAUI相关的UI框架，今天大姚分享一个.NET MAUI开源、免费的UI工具包：Uranium。"
date: 2024-04-11 07:30:14
lastmod: 2024-04-11 07:42:49
author: 追逐时光者
originalTitle: .NET MAUI开源免费的UI工具包 - Uranium
originalLink: https://mp.weixin.qq.com/s/UNhXBQePRmiBBG31jEt2Kg
copyright: Reprinted
draft: false
cover: https://img1.dotnet9.com/2024/04/cover_01.png
banner: false
categories:
  - MAUI
tags:
  - .NET
  - C#
  - MAUI
  - 开源
  - Uranium
---

## **前言**

一直有小伙伴在微信公众号后台留言让我分享一下.NET MAUI 相关的 UI 框架，今天大姚分享一个.NET MAUI 开源、免费的 UI 工具包：Uranium。

![图片](https://img1.dotnet9.com/2024/04/cover_01.png)

## **Uranium 介绍**

Uranium 是一个.NET MAUI 开源免费的 UI 工具包。它提供了一组用于构建现代应用程序的控件和实用程序，它构建在.NET MAUI 基础架构之上，并提供一组控件和布局来构建现代 UI。它还提供了用于在其上构建自定义控件和主题的基础设施。

## **什么是.NET MAUI？**

.NET 多平台应用 UI (.NET MAUI) 是一个跨平台框架，用于使用 C# 和 XAML 创建本机移动和桌面应用。 使用 .NET MAUI，可从单个共享代码库开发可在 Android、iOS、macOS 和 Windows 上运行的应用。

![图片](https://img1.dotnet9.com/2024/04/0101.png)

## **UraniumUI 项目源码查看**

![图片](https://img1.dotnet9.com/2024/04/0102.png)

## **设置`UraniumApp`为启动项目运行**

### **`Windows Machine`运行：**

![图片](https://img1.dotnet9.com/2024/04/0103.png)

![图片](https://img1.dotnet9.com/2024/04/0104.png)

### **`Android Emulator（安卓模拟器）`运行：**

![图片](https://img1.dotnet9.com/2024/04/0105.png)

![图片](https://img1.dotnet9.com/2024/04/0106.png)

![图片](https://img1.dotnet9.com/2024/04/0107.png)

![图片](https://img1.dotnet9.com/2024/04/0108.png)

![图片](https://img1.dotnet9.com/2024/04/0109.png)

#### **安卓模拟器一直卡在不动：**

在某些情况下，在“打开或关闭 Windows 功能”对话框中启用 Hyper-V 和 Windows 虚拟机监控程序平台后可能无法正确启用 Hyper-V。

![图片](https://img1.dotnet9.com/2024/04/0110.png)

我就是开启 Hyper-V 才把安卓模拟器运行起来的。

![图片](https://img1.dotnet9.com/2024/04/0111.png)

- 假如设置了还是不行可以看看微软官方教程：https://learn.microsoft.com/zh-cn/dotnet/maui/android/emulator/troubleshooting?view=net-maui-8.0

#### **错误 APT2000 系统找不到指定的文件：**

文件目录中不能包含中文!!!

![图片](https://img1.dotnet9.com/2024/04/0112.png)

#### **安卓模拟器系统版本需要高版本：**

注意假如安卓模拟器系统版本太低也有可能运行不起来，我选的是最新版！！！

![图片](https://img1.dotnet9.com/2024/04/0113.png)

#### **安卓模拟器运行效果：**

![图片](https://img1.dotnet9.com/2024/04/0114.png)

![图片](https://img1.dotnet9.com/2024/04/0115.png)

![图片](https://img1.dotnet9.com/2024/04/0116.png)

## **安卓模拟器运行效果部分截图**

![图片](https://img1.dotnet9.com/2024/04/0117.png)

![图片](https://img1.dotnet9.com/2024/04/0118.png)

![图片](https://img1.dotnet9.com/2024/04/0119.png)

![图片](https://img1.dotnet9.com/2024/04/0120.png)

![图片](https://img1.dotnet9.com/2024/04/0121.png)

## **Windows 运行效果部分截图**

![图片](https://img1.dotnet9.com/2024/04/0122.png)

![图片](https://img1.dotnet9.com/2024/04/0123.png)

![图片](https://img1.dotnet9.com/2024/04/0124.png)

![图片](https://img1.dotnet9.com/2024/04/0125.png)

![图片](https://img1.dotnet9.com/2024/04/0126.png)

![图片](https://img1.dotnet9.com/2024/04/0127.png)

![图片](https://img1.dotnet9.com/2024/04/0128.png)

![图片](https://img1.dotnet9.com/2024/04/0129.png)

![图片](https://img1.dotnet9.com/2024/04/0130.png)

![图片](https://img1.dotnet9.com/2024/04/0131.png)

![图片](https://img1.dotnet9.com/2024/04/0132.png)

![图片](https://img1.dotnet9.com/2024/04/0133.png)

![图片](https://img1.dotnet9.com/2024/04/0134.png)

## **项目源码地址**

更多项目实用功能和特性欢迎前往项目开源地址查看 👀，别忘了给项目一个 Star 支持 💖。

> https://github.com/enisn/UraniumUI
