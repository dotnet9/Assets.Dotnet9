---
title: .NET 8 SDK安装包可以下载了
slug: Dotnet8-SDK-available-for-release-download
description: .NET 8已经有了安装包提供下载，这是一个好消息，微软给.NET添砖加瓦的动作一直在路上。
date: 2022-08-24 09:17:26
author: 沙漠尽头的狼
draft: false
copyright: Original
cover: https://img1.dotnet9.com/2022/08/cover_02.png
categories: .NET
tags: .NET
---

今早在一个技术交流群看到有`.NET 8`的安装交流，站长下载了，把安装过程记录了，总结是：

>目前还无法 **正常使用** `.NET 8` SDK，虽然可以正常下载、安装，但宇宙第一IDE `VS`还尚未支持，也许站长打开方式不对，欢迎有体验的朋友留言解惑。
>
>**站长猜测：** 微软官方可能要等到11月份`.NET 7`正式版发布后再同步更新VS对`.NET 8`的预览版支持吧，记住这个时间点，这是微软每年对`.NET`版本更新正式发布的时间。

## 下载地址

**微软SDK文档**

微软SDK文档没有提供下载，目前还是 `7.0.0-preview.7`:https://dotnet.microsoft.com/zh-cn/download/dotnet

![](https://img1.dotnet9.com/2022/08/0201.png)

`Github下载`

- [https://github.com/dotnet/installer](https://github.com/dotnet/installer)

![](https://img1.dotnet9.com/2022/08/0202.png)

看下图，`.NET 8`大小与前面几版大小差不了多少：

![](https://img1.dotnet9.com/2022/08/0203.png)

## 安装

安装也没有不同，Windows上直接双击安装：

![](https://img1.dotnet9.com/2022/08/0204.png)

![](https://img1.dotnet9.com/2022/08/0205.png)

站长使用的 `VS 2022- Preview Version 17.4.0 Preview 1.0`，新创建项目没有提供 `.NET 8` 版本选择，直接修改项目工程`.NET版本`也无法编译成功：

![](https://img1.dotnet9.com/2022/08/0206.png)

![](https://img1.dotnet9.com/2022/08/0207.png)

可能还需要IDE更新加持吧，目前没有最新的VS更新提示，但 `.NET 8` 已经有了安装包提供下载，这是一个好消息，说明微软给 `.NET` 添砖加瓦的动作一直在路上。