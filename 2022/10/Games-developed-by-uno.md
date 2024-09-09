---
title: Uno开发的小游戏
slug: Games-developed-by-uno
description: 一直以为uno只能开发桌面和移动App，原来它也能开发Web，而且这还是个Web小游戏！
date: 2022-10-24 21:19:47
copyright: Original
author: 沙漠尽头的狼
draft: false
cover: https://img1.dotnet9.com/2022/10/0701.png
categories: 
    - .NET
tags: 
    - Uno
---

大家好，我是沙漠尽头的狼。

刚在微信群里逛，有网友发了Uno的在线小游戏，站长觉得不错，简单分享下：

![群聊涨见识](https://img1.dotnet9.com/2022/10/0704.png)

## Uno是什么？

使用 C# 和 WinUI 实现像素完美的多平台应用程序，用于构建适用于 Windows、iOS、Android、WebAssembly、macOS 和 Linux 的单一代码库应用程序的开源 UI 平台。

## 在线小游戏

在线地址：https://asadullahrifat89.github.io/hungry-worm-uno-platform/

由于是使用 [Web Assembly](https://baike.baidu.com/item/WebAssembly/61812997?fr=aladdin)开发的，虽然有着“快速、高效、可移植——通过利用常见的硬件能力，WebAssembly 代码在不同平台上能够以接近本地速度运行。”，但由于Uno也是带有运行时，所以网站体积有点大，28MB左右，加载10s左右，有科学工具的建议打开玩耍。

> 简单普及什么是Web Assembly：面向Web的二进制格式，WebAssembly（简称wasm）是一个虚拟指令集体系架构（virtual ISA），整体架构包括核心的ISA定义、[二进制编码](https://baike.baidu.com/item/二进制编码/1758517?fromModule=lemma_inlink)、程序语义的定义与执行，以及面向不同的嵌入环境（如Web）的应用[编程接口](https://baike.baidu.com/item/编程接口/3339711?fromModule=lemma_inlink)（WebAssembly API）。其初始目标是为[C](https://baike.baidu.com/item/C/7252092?fromModule=lemma_inlink)/[C++](https://baike.baidu.com/item/C%2B%2B/99272?fromModule=lemma_inlink)等语言编写的程序经过编译，在确保安全和接近原生应用的运行速度更好地在[Web](https://baike.baidu.com/item/Web/150564?fromModule=lemma_inlink)平台上运行。   - 摘自百度百科

**网站体积**

![网站体积](https://img1.dotnet9.com/2022/10/0705.png)

**游戏加载页**

因网络环境不同，网站体积稍大，所以有个加载页面不至于等待无聊：

![游戏加载页](https://img1.dotnet9.com/2022/10/0701.png)

**游戏首页**

![游戏首页](https://img1.dotnet9.com/2022/10/0702.gif)

**小玩一把**

![小玩一把](https://img1.dotnet9.com/2022/10/0703.gif)

## **Uno官网**

https://platform.uno/

![Uno官网](https://img1.dotnet9.com/2022/10/0706.png)

## Uno仓库

https://github.com/nventive/Uno

![Uno仓库](https://img1.dotnet9.com/2022/10/0707.png)

从代码最后提交时间，可以看出此框架很活跃，是MAUI的有力竞争对手，有兴趣的去看看吧。