---
title: 既能做为工具使用，又能学习它源码的.NET开源项目-SmartSQL
slug: Dotnet-open-source-project-that-can-be-used-as-a-tool-and-learn-its-source-code-SmartSQL
description: 一款方便、快捷的数据库文档查询、生成工具，致力于成为帮助企业快速实现数字化转型的元数据管理工具。
date: 2023-02-12 18:23:35
copyright: Default
author: 沙漠尽头的狼
draft: false
cover: https://img1.dotnet9.com/2023/02/cover_04.png
categories: WPF
albums: 开源WPF
tags: WPF,数据库管理工具,开源项目
---

![项目宣传图](https://img1.dotnet9.com/2023/02/cover_04.png)

大家好，我是沙漠尽头的狼。

## 1. 项目简介

今天介绍一个.NET开源项目 [SmartSQL](https://gitee.com/dotnetchina/SmartSQL)，站长是从张队分享的一篇公众号文章[开源：一款基于.Net开发提升开发效率的强大多功能工具箱](https://mp.weixin.qq.com/s/Sck5f3fsPt4zszF_xqIg9Q)了解到的，今天通过查看该项目源码，非常值得二次推荐，本文从源码及功能两点介绍。

关于该开源项目：

- 仓库地址：https://gitee.com/dotnetchina/SmartSQL
- 开源协议：[Apache-2.0](https://gitee.com/dotnetchina/SmartSQL/blob/master/LICENSE)
- 项目目标：一款方便、快捷的数据库文档查询、生成工具，致力于成为帮助企业快速实现数字化转型的元数据管理工具。

![项目仓库](https://img1.dotnet9.com/2023/02/0401.png)

## 2. 源码简单分析

![项目解决方案](https://img1.dotnet9.com/2023/02/0402.png)

源码包含三个工程：SmartSQL、SmartSQL.DocUtils和SmartSQL.Framework，我们简单拉一遍，对源码感兴趣的朋友可以拉源码查看哦。

### 2.1. SmartSQL

![主工程](https://img1.dotnet9.com/2023/02/0403.png)

这是主工程，是一个WPF项目，里面使用了[AduSkin](https://dotnet9.com/2020/02/Open-source-Csharp-WPF-control-library-AduSkin-UI)、[AvalonEdit](https://github.com/icsharpcode/AvalonEdit)、[HandyControl](https://dotnet9.com/2019/12/Open-source-WPF-control-library-handycontrol)、[FontAwesome.WPF](https://github.com/charri/Font-Awesome-WPF)等第三方库，通过该工程可以学习怎么使用第三方控件库、字体库等，后面通过看工具截图可看控件库的实用效果。

![项目工具代码截图](https://img1.dotnet9.com/2023/02/0406.png)

另外如上图代码文件截图，每个工具具体的实现也是在这个工程中，平时工作中如果有相关的功能需求可以直接参考该项目，工具一览如下。

![项目工具一览](https://img1.dotnet9.com/2023/02/0411.png)

### 2.2. SmartSQL.DocUtils

![SmartSQL.DocUtils工程](https://img1.dotnet9.com/2023/02/0404.png)

该工程是一个类库，封装了各种数据文件的导入与导出，通过仓库介绍你就知道支持的文件有多丰富了：

>SmartSQL 是一款方便、快捷的数据库文档查询、导出工具！从最初仅支持SqlServer数据库、CHM文档格式开始，通过不断地探索开发、集思广益和不断改进，又陆续支持Word、Excel、PDF、Html、Xml、Json、MarkDown等文档格式的导出。同时又扩展支持包括SqlServer、MySql、PostgreSQL、SQLite等多种数据库的文档查询和导出功能。

### 2.3. SmartSQL.Framework

![SmartSQL.Framework工程](https://img1.dotnet9.com/2023/02/0405.png)

从名字可知，该类库是此项目的核心工程，即数据库文档查询、导出的实现核心代码库，对数据库操作的实现感兴趣的同志可以查看。

## 3. 功能展示

设置`SmartSQL`工程为启动项目，点击运行（也可[下载安装包](https://gitee.com/dotnetchina/SmartSQL/releases)运行）：

![运行项目源码](https://img1.dotnet9.com/2023/02/0407.gif)

下面列出部分功能截图(基本来自仓库readme)，详细功能请看[仓库](https://gitee.com/dotnetchina/SmartSQL)实时更新。

### 3.1. 功能架构

![功能架构](https://img1.dotnet9.com/2023/02/0408.jpg)

### 3.2. Dashboard

![Dashboard](https://img1.dotnet9.com/2023/02/0409.png)

### 3.3. 快捷查询

![快捷查询](https://img1.dotnet9.com/2023/02/0412.png)

### 3.4. 导入导出

![导入导出](https://img1.dotnet9.com/2023/02/0413.png)

### 3.5. 文档截图

**CHM文档**

![CHM文档](https://img1.dotnet9.com/2023/02/0414.png)

**Html文档**

![Html文档](https://img1.dotnet9.com/2023/02/0415.png)

**Word文档**

![Word文档](https://img1.dotnet9.com/2023/02/0416.png)

**Excel文档**

![Excel文档](https://img1.dotnet9.com/2023/02/0417.png)

**PDF文档**

![PDF文档](https://img1.dotnet9.com/2023/02/0418.png)

### 3.6. 工具箱列表

![工具箱列表](https://img1.dotnet9.com/2023/02/0410.png)

录了3个工具的使用：

**二维码生成**

可扫码试试哟。

![二维码生成](https://img1.dotnet9.com/2023/02/0419.gif)

**Json格式化**

![Json格式化](https://img1.dotnet9.com/2023/02/0420.gif)

**汉字转拼音**

![汉字转拼音](https://img1.dotnet9.com/2023/02/0421.gif)

更多工具请编译[源码](https://gitee.com/dotnetchina/SmartSQ)或[下载](https://gitee.com/dotnetchina/SmartSQL/releases)体验。

## 4. 结尾

最后再给出仓库地址：https://gitee.com/dotnetchina/SmartSQL。

希望该工具给您带来便利，工具源码给您带来参考。