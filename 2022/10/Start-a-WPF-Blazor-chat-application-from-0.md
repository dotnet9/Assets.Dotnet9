---
title: 从0开始做一个WPF+Blazor对话小程序
slug: Start-a-WPF-Blazor-chat-application-from-0
description: 从一个WPF Hello World程序开始，逐渐引入Blazor，做个免费能看的对话小程序耍耍。
date: 2022-10-28 22:06:32
copyright: Default
author: 沙漠尽头的狼
draft: false
cover: https://img1.dotnet9.com/2022/10/2-chat-window.png
categories: WPF,Blazor
---

大家好，我是沙漠尽头的狼。

>.NET是免费，跨平台，开源，用于构建所有应用的开发人员平台。

本文演示如何在WPF中使用Blazor为客户端开发注入新活力。

**注**

要使WPF支持Blazor，.NET版本必须是 .NET 6.0 或更高版本，本示例使用的.NET 7.0，版本要求见[链接](https://learn.microsoft.com/zh-cn/aspnet/core/blazor/hybrid/tutorials/wpf?view=aspnetcore-7.0)：

![.NET版本要求](https://img1.dotnet9.com/2022/10/1003.png)

## 1. WPF默认程序

使用WPF模板创建一个默认程序，取名【WPFBlazorChat】，项目组织结构如下：

![空白WPF项目](https://img1.dotnet9.com/2022/10/1001.png)

运行项目，一个空白窗口：

![WPF项目空白窗口](https://img1.dotnet9.com/2022/10/1002.png)

接着我们添加Blazor支持。

## 2. 添加Blazor支持

依然使用上面的工程，添加Blazor支持，此部分参考微软文档[生成 Windows Presentation Foundation (WPF) Blazor 应用](https://learn.microsoft.com/zh-cn/aspnet/core/blazor/hybrid/tutorials/wpf?view=aspnetcore-7.0)，本小节快速略过。




## 3. 自定义窗体

### 3.1 WPF原生实现

### 3.2 Blazor实现标题栏

## 4. 添加第三方Blazor组件

## 5. 多窗体消息通知

### 5.1 单例实现通知

#### 5.1.1 属性、方法、委托

### 5.2 定义一个Messager

## 6. 实现本文示例

## 7. Click Once发布尝试

参考：[《快速创建软件安装包-ClickOnce》](https://mp.weixin.qq.com/s/zcO1J-AqiK7LkU52MRwmqw)

## 8. Q&A

- 8.1 BlazorWebView的竖直滚动条怎么回事？

隐藏BlazorWebView滚动条

- 窗体拖动：https://github.com/James231/BlazorDesktopWPF-CustomTitleBar

- 8.2 WPF + Blazor支持哪些操作系统

支持Windows 7，这是本文示例Click Once安装页面：https://dotnet9.com/WPFBlazorChat

- 8.3 为啥要在WPF里使用Blazor？吃饱了撑的？

- 8.4 Blazor还有哪些框架可以使用？

- 8.5 本文示例代码能给我不？

