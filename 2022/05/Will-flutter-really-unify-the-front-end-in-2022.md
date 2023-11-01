---
title: 2022年Flutter真的会一统大前端吗？
slug: Will-flutter-really-unify-the-front-end-in-2022
description: 在创建 iOS 和 Android 应用程序时，通常推荐使用 Flutter，因为使用它更加简单高效。
date: 2022-05-06 06:28:41
copyright: Reprinted
author: 坚果
originaltitle: 2022年Flutter真的会一统大前端吗？
originallink: https://www.aisoutu.com/a/1610740
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_19.png
categories: Flutter
---

副标题《理性对待Flutter》

>作者：坚果

在创建 iOS 和 Android 应用程序时，通常推荐使用 Flutter，因为使用它更加简单高效。正是由于 Flutter 的诸多优势，它在许多情况下都是移动应用程序的绝佳候选者。它的性能、逻辑架构和文档都备受推崇。国内的社区也非常的活跃，但在某些情况下，Flutter 可能并不是最合适的。这就是我们将在本博客中看到的内容。让我们看一些场景..

## 1. 当你的项目依赖于特定设备和平台的主要库时

如果您的项目需要 Wear OS 版本或 Smart TV 应用程序，您会遇到一些问题。你可以在技术上为这些平台构建一个 Flutter 应用程序。但是，Wear OS 并不支持 Flutter 的许多开发功能。所以会给你带来困扰。

对于 Android TV，您必须从头开始使用控制逻辑。因为 Android TV 只读取远程控制输入，而 Flutter 使用触摸屏和鼠标移动，情况就是这样，孰轻孰重，自己考量。

## 2. 当您的应用程序对应用大小要求很高时

由于flutter不是原生的，它在应用程序之上添加了一些其他库来工作。如果每个字节对您的应用程序都很重要时，您可能需要在原生平台上进行开发。由于它具有内置的小部件而不是使用原生平台小部件，因此 Flutter 应用程序的最小大小超过 4MB，明显大于原生 Java（539KB）和 Kotlin（550KB）应用程序。

老实说，它的竞争对手也有同样的问题， React Native 占用 7MB。

但是由于硬件技术的进步，即使是智能手机也配备了更大的内存和存储空间。所以大多数人并不关心应用程序的大小。

## 3. 硬件支持

![](https://img1.dotnet9.com/2022/05/cover_19.png)

不建议将 Flutter 用于通过蓝牙连接到硬件设备的应用程序。由于它本身不使用设备的蓝牙，因此会出现一些连接问题和性能问题。

## 4. Flutter for Web

它不是html。是的，即使是 Web 版 Flutter 也已正式发布，但是它不会撼动互联网世界。市场上有许多简单有效的库来开发网站。当涉及到网站、页面加载速度、SEO、性能和一切都很重要时，Flutter 很难通过简单的 dart to Js Engine 来实现这些。

但现在判断还为时过早。Flutter 可能会拿出精彩的优化性能。让我们敬请期待，在王叔的视频里，对此类问题也做过阐述，地址在这儿。

Flutter可以做网站吗｜Flutter Web劝退指南｜从入门到放弃只需要几分钟

## 5. 平台特定的外观和设计

Material Widgets 和 Cupertino 小部件分别是 Android 和 iOS 应用程序的两种不同的构建块。在创建 Flutter 应用程序时，您可以同时使用这两个小部件，但是当我们为 iOS 构建使用 Material 小部件时，该应用程序缺乏原生的外观和感觉。为了实现这两个应用程序的原生外观，我们应该检查代码中的平台并渲染特定的小部件，这是编码和应用程序性能最差的部分。

![](https://img1.dotnet9.com/2022/05/1901.png)

## 6. 缺乏第三方集成

尽管 Flutter 有 19k+ 的库和插件，但它依旧缺少许多流行的库和 SDK。目前正在开发许多包并迁移到 Flutter。如果您要开发一个主要依赖第三方插件的应用程序，请检查 SDK 的最新版本是否适用于 Flutter。至于如何检查，

此外，始终首选积极维护的存储库。

最后，Flutter 并不总是很棒。事实是它无法一碗水端平。当然这只是决定把它放在哪里的问题。Flutter 依旧可以简便，高效地使用。