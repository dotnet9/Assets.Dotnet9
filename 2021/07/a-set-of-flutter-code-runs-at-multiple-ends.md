---
title: 一套Flutter代码多端运行
slug: a-set-of-flutter-code-runs-at-multiple-ends
description: 本文不打算介绍功能代码，分享一个油管关于Flutter的分享视频
date: 2021-07-10 23:29:38
copyright: Original
originalTitle: 一套Flutter代码多端运行
draft: False
cover: https://img1.dotnet9.com/2021/07/cover_02.png
categories: 
    - Flutter
tags: 
    - Flutter
    - Mac
    - iOS
    - Android
    - Web
    - Desktop
---

> 参考视频：[https://www.youtube.com/watch?v=\_uOgXpEHNbc](https://www.youtube.com/watch?v=_uOgXpEHNbc)
>
> 视频配套源码：[https://github.com/abuanwar072/Flutter-Responsive-Admin-Panel-or-Dashboard](https://github.com/abuanwar072/Flutter-Responsive-Admin-Panel-or-Dashboard)
>
> 文中截图源码(站长照着视频学习码的)：[https://github.com/dotnet9/FlutterTest/tree/main/src/admin_panel](https://github.com/dotnet9/FlutterTest/tree/main/src/admin_panel)

本文不打算介绍功能代码，大家如感兴趣可点击上面的视频或者源码参考。

![代码结构](https://img1.dotnet9.com/2021/07/0201.jpeg)

看代码文件多，其实每个 dart 文件代码只有几十行而已。面板分为各个小模块 dart 文件，最后组合一起展示。

先看看 mac 桌面展示(Windows 桌面类似)：

![桌面](https://img1.dotnet9.com/2021/07/0202.jpeg)

网页版（调用 F12，和前端一样的调试）

![网页](https://img1.dotnet9.com/2021/07/0203.jpeg)

iOS（iPhone 12 Pro Max 模拟器）

<video id="video" controls="" preload="none" poster="https://img1.dotnet9.com/2021/07/0204.png">
  <source id="mp4" src="https://img1.dotnet9.com/2021/07/0204.mp4" type="video/mp4">
</video>

android(模拟器)

<video id="video" controls="" preload="none" poster="https://img1.dotnet9.com/2021/07/0205.png">
  <source id="mp4" src="https://img1.dotnet9.com/2021/07/0205.mp4" type="video/mp4">
</video>

原视频录制一小段

<video id="video" controls="" preload="none" poster="https://img1.dotnet9.com/2021/07/0206.png">
  <source id="mp4" src="https://img1.dotnet9.com/2021/07/0206.mp4" type="video/mp4">
</video>

是否感兴趣？回到文章开头，点击视频（或源码）学习吧。
