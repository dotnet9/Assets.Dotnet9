---
title: 快速创建软件安装包-ClickOnce
slug: Quickly-create-software-installation-package-ClickOnce
description: ClickOnce 是一种部署技术，使用该技术可创建自行更新的基于 Windows 的应用程序，这些应用程序可以通过最低程度的用户交互来安装和运行。
date: 2022-11-02 20:56:48
copyright: Original
author: 沙漠尽头的狼
draft: false
cover: https://img1.dotnet9.com/2022/11/cover_02.png
categories: 
    - .NET
tags: 
    - .NET
---

大家好，我是沙漠尽头的狼。

>.NET是免费，跨平台，开源，用于构建所有应用的开发人员平台。

今天介绍使用ClickOnce制作软件安装包，首先我们先了解什么是ClickOne。

## 1. 什么是ClickOnce

以下段落摘自微软文档：https://learn.microsoft.com/zh-cn/visualstudio/deployment/clickonce-security-and-deployment?view=vs-2022。

---

ClickOnce 是一种部署技术，使用该技术可创建自行更新的基于 Windows 的应用程序，这些应用程序可以通过最低程度的用户交互来安装和运行。

ClickOnce 部署克服了部署中所固有的三个主要问题：

1. 更新应用程序的困难

使用 Microsoft Windows Installer 部署，每次应用程序更新，用户都必须重新安装整个应用程序；使用 ClickOnce 部署，则可以自动提供更新。只有更改过的应用程序部分才会被下载，然后从新的并行文件夹重新安装完整的、更新后的应用程序。

2. 对用户的计算机的影响

使用 Windows Installer 部署时，应用程序通常依赖于共享组件，这便有可能发生版本冲突；而使用 ClickOnce 部署时，每个应用程序都是独立的，不会干扰其他应用程序。

3. 安全权限

Windows Installer 部署要求管理员权限并且只允许受限制的用户安装；而 ClickOnce 部署允许非管理用户安装应用程序并仅授予应用程序所需要的那些代码访问安全权限。

过去，这些问题有时会使开发人员决定创建 Web 应用程序而不是基于 Windows 的应用程序，为便于安装而牺牲了 Windows窗体丰富的用户界面和响应性。对于使用 ClickOnce 部署的应用程序，您可以集这两种技术的优势于一身。

## 2. 使用ClickOnce创建安装包

### 2.1 需要服务器

首先，我们需要一个线上的网站，用于托管软件更新文件，比如在[Dotnet9](https://dotnet9.com)网站的根目录创建一个`WPFBlazorChat`的目录，那么线上托管地址则是`https://dotnet9.com/WPFBlazorChat`，目录如下：

![](https://img1.dotnet9.com/2022/11/0201.png)

### 2.2 开始制作安装包

记住上面的线上地址，使用前几天介绍的`WPFBlazorChat`做为示例做安装包，仓库地址是：https://github.com/dotnet9/WPFBlazorChat，所以上面创建的目录与项目名同名：`WPFBlazorChat`。

1. 选择`WPFBlazorChat`工程，右键发布

![](https://img1.dotnet9.com/2022/11/0202.png)

2. 在弹出的界面，选择ClickOnce，点击下一步

![](https://img1.dotnet9.com/2022/11/0203.png)

3. 发布位置随意

![](https://img1.dotnet9.com/2022/11/0204.png)

4. 选择软件安装包获取地址

![](https://img1.dotnet9.com/2022/11/0205.png)

5. 设置

- 可配置程序运行时自动检测更新、软件版本号等，如下图：

![](https://img1.dotnet9.com/2022/11/0206.png)

如上图，如果勾选【自动递增修订号】，那么每次点击发布，修订号会递增（感觉说的是废话，主要是方便版本号管理）。

- 点击应用程序文件，可勾选哪些文件可以不用下载，如下图：

![](https://img1.dotnet9.com/2022/11/0207.png)

- 选择先决条件，即选择程序的运行时，因为程序默认支持.NET 6和.NET 7，所以站长勾选了.NET 7 x64，win7 32位的同学如有需要，按需选择：

![](https://img1.dotnet9.com/2022/11/0208.png)

- 选项配置

配置软件安装包信息，其中比较重要的是发布者名称和套件名称，决定软件程序释放位置：

![](https://img1.dotnet9.com/2022/11/0209.png)

![](https://img1.dotnet9.com/2022/11/0223.png)

部署文件配置，其中Publish.html配置了安装包下载页面

![](https://img1.dotnet9.com/2022/11/0210.png)

6. 签名清单

未设置，直接下一步：

![](https://img1.dotnet9.com/2022/11/0211.png)

7. 程序发布配置

按情况选择，站长选择的.NET 7 64位发布，`注意需要和前面选择.NET桌面运行时版本一致`：

![](https://img1.dotnet9.com/2022/11/0212.png)

8. 点击发布

最后一个操作，点击发布

![](https://img1.dotnet9.com/2022/11/0213.png)

发布完成，点击【发布位置】路径：

![](https://img1.dotnet9.com/2022/11/0214.png)

### 2.3 上传

上面制作了软件安装包，还差一个步骤，就是把安装包丢网站上去，这个就比较简单了，前提是网站已经部署了哈：

![](https://img1.dotnet9.com/2022/11/0215.png)

![](https://img1.dotnet9.com/2022/11/0216.gif)

### 2.4 程序安装、运行

地址是：https://dotnet9.com/WPFBlazorChat/Publish.html

![](https://img1.dotnet9.com/2022/11/0217.png)

如上图，显示了我们创建安装包配置的软件安装包名称、版本号、发布者、需要的.NET运行时版本等，点击【安装】按钮，会下载一个`setup.exe`安装文件，这个文件很小，666KB，好吉利的数字：

![](https://img1.dotnet9.com/2022/11/0218.png)

运行`setup.exe`，会自动从上面的服务器(https://dotnet9.com/WPFBlazorChat/)中检测版本号、文件更新情况，自动下载程序文件了：

下图是服务器软件安装包信息：

![](https://img1.dotnet9.com/2022/11/0224.gif)

下图是安装过程截图：

![](https://img1.dotnet9.com/2022/11/0219.gif)

安装包下载完成后，程序自动运行，下面就是测试程序运行界面了，WPF Blazor开发的哟，点击戳[源码](https://github.com/dotnet9/WPFBlazorChat)：

![](https://img1.dotnet9.com/2022/11/0220.gif)

## 3. Q&A

1. ClickOnce 部署的工作原理

核心 ClickOnce 部署体系结构基于两个 XML 清单文件：应用程序清单和部署清单。 这些文件用于描述从哪里安装 ClickOnce 应用程序、如何更新这些应用程序以及何时更新它们。

更多请访问微软文档：https://learn.microsoft.com/zh-cn/visualstudio/deployment/clickonce-security-and-deployment?view=vs-2022

本文完，下篇介绍WPF中如何使用Blazor开发应用。