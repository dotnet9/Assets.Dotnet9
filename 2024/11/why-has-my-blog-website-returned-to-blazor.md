---
title: 我的博客网站为什么又回归Blazor了
slug: why-has-my-blog-website-returned-to-blazor
description: 博客网站开发历经艰辛，尝试过MVC、Vue、Go等近10个版本，如今回归Blazor并采用静态SSR，速度飞涨，已成功上线。
date: 2024-11-06 07:14:26
lastmod: 2024-11-06 08:04:16
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2024/11/0201.png
categories: 
    - .NET
tags: 
    - C#
    - Blazor
    - 开源博客
    - 博客系统
    - 静态SSR
---

## 引言

站长先前尝试过MVC、Razor Pages、Vue、Go、Blazor，折腾不下10个版本，现在又回归Blazor，使用了静态SSR，访问速度飞快，感谢以下开源项目：

- Known: https://known.org.cn/

>开源企业级开发框架，基于 Blazor 技术实现，低代码，跨平台，开箱即用，一处代码，多处运行。以高效、‌灵活为核心，‌重塑软件开发模式，‌助您轻松应对数字化转型挑战，‌开启业务增长新篇章。‌‌

**什么是静态SSR？**

静态SSR不同于Blazor Server或Blazor Client（WASM），我们看[微软文档](https://learn.microsoft.com/zh-cn/aspnet/core/blazor/components/class-libraries-and-static-server-side-rendering?view=aspnetcore-9.0)解释：

>静态 SSR 是一种组件在服务器处理传入 HTTP 请求时运行的模式。 Blazor 将组件呈现为 HTML，并包含在响应中。 发送响应后，将丢弃服务器端组件和呈现器状态，因此浏览器中只剩下 HTML。

>这种模式的好处是托管成本更低且更具可缩放性，因为不需要持续的服务器资源来保存组件状态，浏览器和服务器之间不需要维护持续的连接，并且浏览器中不需要 WebAssembly 负载。

## 网站功能说明

### 首页

- https://dotnet9.com

![](https://img1.dotnet9.com/2024/11/0202.gif)

### 文档

介绍网站部分开源项目使用，下面是部分项目简介：

- https://dotnet9.com/doc

![](https://img1.dotnet9.com/2024/11/0203.gif)

1. CodeWF

本站源码仓库，点击[链接](https://github.com/dotnet9/CodeWF)查看。

2. CodeWF.EventBus

适用于进程内事件传递（无其他外部依赖），与 MediatR 功能类似，点击[链接](https://github.com/dotnet9/CodeWF.EventBus)查看。

3. CodeWF.EventBus.Socket

CodeWF.EventBus.Socket 是一个轻量级的、基于 Socket 的分布式事件总线系统，旨在简化分布式架构中的事件通信。它允许进程之间通过发布/订阅模式进行通信，无需依赖外部消息队列服务。

点击[链接](https://github.com/dotnet9/CodeWF.EventBus.Socket)查看。

4. CodeWF.NetWeaver

CodeWF.NetWeaver 是一个简洁而强大的C#库，支持AOT，用于处理TCP和UDP数据包的组包和解包操作。点击[链接](https://github.com/dotnet9/CodeWF.NetWeaver)查看。

5. CodeWF.Toolbox

CodeWF Toolbox 使用Avalonia开发的跨平台工具箱，使用了Prism做为模块化开发框架，支持AOT发布，可做为Avalonia项目学习。点击[链接](https://github.com/dotnet9/CodeWF.Toolbox)查看。

6. CodeWF.Tools

收集、分享常用工具类。点击[链接](https://github.com/dotnet9/CodeWF.Tools)查看。

7. Assets.Dotnet9

本站的核心数据仓库，点击[链接](https://github.com/dotnet9/Assets.Dotnet9)查看。

### 博客

- https://dotnet9.com/bbs/

博客页面就是标准的技术博客风格样式：

![](https://img1.dotnet9.com/2024/11/0204.png)

这里要感谢[VleaStwo]([VleaStwo (Lee)](https://github.com/VleaStwo))大佬提供的TOC功能

![](https://img1.dotnet9.com/2024/11/0205.gif)

## 总结

[VleaStwo]([VleaStwo (Lee)](https://github.com/VleaStwo))大佬开了一个Masa分支，有兴趣的朋友欢迎PR或交流：

![](https://img1.dotnet9.com/2024/11/0206.png)