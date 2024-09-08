---
title: 一个WPF开发的、界面简洁漂亮的音频播放器
slug: A-simple-and-beautiful-audio-player-developed-by-WPF
description: 推荐一个界面简洁、美观的、支持国际化开源音频播放器。
date: 2023-07-17 20:31:30
copyright: Reprinted
author: 编程乐趣
originalTitle: 一个WPF开发的、界面简洁漂亮的音频播放器
originalLink: https://mp.weixin.qq.com/s/3nioR2zleQKWWAfkvgOxMg
draft: false
cover: https://img1.dotnet9.com/2023/07/0602.png
categories: .NET
tags: .NET
---

今天推荐一个界面简洁、美观的、支持国际化开源音频播放器。

## 项目简介

这是一个基于 C# + WPF 开发的，界面外观简洁大方，操作体验良好的音频播放器。

支持各种音频格式，包括：MP4、WMA、OGG、FLAC、M4A、AAC、WAV、APE 和 OPUS；支持标记、实时显示歌词等功能；支持换肤、中英文等主流语言。

该播放器直接使用，或者用于学习都是非常不错的选择。

## 技术架构

1. 平台：采用.Net Framework 4.7 开发，支持 Windows；
2. 依赖 Windows.winmd，支持 Win10+；更低的平台需要安装相关依赖、或者去除部分功能才能编译成功；
3. 核心音频处理采用 FFmpeg 组件。

## 项目结构

![](https://img1.dotnet9.com/2023/07/0601.png)

## 界面截图

![](https://img1.dotnet9.com/2023/07/0602.png)

## 项目地址

https://github.com/digimezzo/dopamine-windows
