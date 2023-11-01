---
title: Dotnet9网站又重构上线了：这次回归简约风！
slug: Dotnet9-website-has-been-restructured-and-launched-again-this-time-it-is-back-to-minimalist-style
description: 网站已经进行了重构，前台采用了简约风格，以提供更好的用户体验。
date: 2023-04-23 08:28:13
lastmod: 2023-04-30 08:40:13
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2023/04/cover_03.jpg
categories: Web API
tags: Dotnet9,MASA Framework,Razor Pages,EF Core
---

![](https://img1.dotnet9.com/2023/04/cover_03.jpg)

大家好，我是沙漠尽头的狼。

我的网站Dotnet9 (https://dotnet9.com) 进行了新一轮的重构：前台由Vue 3换回[ASP.NET Core Razor Pages](https://learn.microsoft.com/zh-cn/aspnet/core/razor-pages/?view=aspnetcore-8.0&tabs=visual-studio)，风格以简约为主，主打内容为王，放弃花哨，网友称风格类似早期博客园，站长其实买的杨青青个人博客（https://www.yangqq.com/）的静态模板；后端采用MASA Framework搭建，框架地址是 https://www.masastack.com/framework，后端依然以DDD设计为开发指导，这次加入了CQRS。开发总体规划是：后端框架采用MASA Framework应该是不变了，并且前后台现在全面拥抱了 .NET 8。

对了，网站开源地址是：https://github.com/dotnet9/dotnet9 。


## 怎么又重构了？

随着技术的不断发展，网站的重构已经成为了一个必然的趋势。为了更好地满足个人学习的需求，提高网站的性能和用户体验，Dotnet9网站进行了新一轮的重构。本次重构主要包括前台和后台两个方面。

### 前台重构

技术栈：ASP.NET Core 8.0 Razor Pages

在前台方面，Dotnet9网站将原来的 Vue 3 换回了 [Razor Pages](https://learn.microsoft.com/zh-cn/aspnet/core/razor-pages/?view=aspnetcore-8.0&tabs=visual-studio) 。这是因为Vue 3虽然有很多优点，但是在性能和SEO方面还存在一些问题。而Razor Pages则更加适合于构建前台网站（服务端渲染），具有更好的性能和SEO优化效果。

同时，Dotnet9网站在风格上也进行了一些调整。网站的风格以简约为主，放弃了过多的花哨效果，更加注重内容的呈现。这种风格类似于早期的博客园，让用户更加专注于阅读和学习。

### 后台重构

技术栈：ASP.NET Core 8.0 Web API ( MASA Framework + EF Core 8.0(PostgreSQL), DDD + CQRS)

在后台后端方面，Dotnet9网站采用了 [MASA Framework](https://www.masastack.com/framework) 作为开发框架。[MASA Framework](https://www.masastack.com/framework) 是.NET下一代微服务开发框架, 助力开发者和企业开启全新的现代化应用开发交付体验。

在开发设计上，Dotnet9网站依然采用了DDD（领域驱动设计）的思想实践。这种设计思想可以帮助开发者更好地理解业务需求，将业务逻辑和技术实现分离开来，从而提高代码的可维护性和可扩展性。

此外，Dotnet9网站还加入了CQRS（命令查询职责分离）的设计模式，由 [MASA Framework](https://www.masastack.com/framework) 提供技术支持。CQRS是一种与领域驱动设计(DDD)和事件溯源相关的架构模式，它将事件（Event）划分为 命令端（Command）和 查询端（Query），可以提高系统的性能和可扩展性。在Dotnet9网站中，博客文章的查询就使用了查询（Query），文章阅读统计（开发中）使用了命令（Command）。

### 小结

Dotnet9网站的重构，不仅提高了网站的性能和用户体验，还采用了最新的技术和设计思想，使得网站更加具有可维护性和可扩展性。在未来的发展中，Dotnet9网站将继续秉承这种理念，不断优化和改进，为用户提供更好的服务，当然主要以个人学习、不断演进为主。

## 成果展示

首页：

![首页](https://img1.dotnet9.com/2023/04/0301.png)

文章专辑：

![文章专辑](https://img1.dotnet9.com/2023/04/0303.png)

文章详情：

![文章详情](https://img1.dotnet9.com/2023/04/0302.png)

## 源码

这次把历史分支也做了清理，只保留develop和main分支。

仓库：https://github.com/dotnet9/dotnet9

解决方案结构如下：

![解决方案结构](https://img1.dotnet9.com/2023/04/0304.png)

前台主工程：Dotnet9.RazorPages

![Dotnet9.RazorPages](https://img1.dotnet9.com/2023/04/0306.png)

后端主工程：Dotnet9.Service

![Dotnet9.Service](https://img1.dotnet9.com/2023/04/0305.png)

1. Dotnet9.Commons：工具库
2. Dotnet9.Contracts：暂时放Dto类
3. Dotnet9.RazorPages：前台主工程，逐步完善
4. Dotnet9.Service：后端主工程，暂时将各种分层文件放一个工程，有需要再分库
5. Dotnet9.Admin：后台前端暂定

等网站开发完成，写个Dotnet9网站前后台开发系列教程分享，不是今年，就是明年....

本文就到这里，去旅游了....

## 技术交流

- 微信公众号如下：Dotnet9
- 微信技术交流群：添加微信（dotnet9com）备注“入群”
- QQ技术交流群：771992300。

![](https://img1.dotnet9.com/site/knowledgeplanet.jpg)