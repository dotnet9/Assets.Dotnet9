---
title: 怎么制作炫酷软件安装包
slug: How-to-make-cool-software-installation-package
description: 介绍一款非常棒的软件程序打包工具
date: 2022-05-06 06:53:32
copyright: Default
originaltitle: 怎么制作炫酷软件安装包
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_21.png
categories: 软件分享
---

>本文不是广告，刚开始写这篇分享时，以为有免费体验的，普通用户可以使用，最多就是特殊功能需要付费，没想到`所有功能都是付费的`。

## 前言

站长发过一篇[《怎么做一个专业的软件安装包？》](https://dotnet9.com/2021/02/how-to-make-a-professional-software-installation-package)，今天介绍另一款软件安装包制作软件，特点是安装、卸载效果特别酷绚：火凤打包工具（[hofoSetup](https://hofosoft.cn/))，该软件由魔音作者推荐：[魔音](http://feiyu.vin/)。

## 1. 准备工作

### 1.1 准备工具

- 官网：[http://www.hofosoft.com](http://www.hofosoft.com)
- 下载：[https://www.hofosoft.cn/download/setup.zip](https://www.hofosoft.cn/download/setup.zip)

![](https://img1.dotnet9.com/2022/05/2114.png) 

**注意：下载时，操作系统可能有如下提示**

![](https://img1.dotnet9.com/2022/05/2102.png) 

这是误报，先添加一下信任：

![](https://img1.dotnet9.com/2022/05/2103.png) 

上面的操作貌似没用，Windows实时保护暂时关闭一下：[参考](http://www.xitongzhijia.net/xtjc/20200610/181048.html)

![](https://img1.dotnet9.com/2022/05/2104.png) 

### 1.2 准备项目

站长依然使用[乐趣课堂](https://github.com/dotnet9/lqclass.com)项目做为测试，当然你可以使用其他类型的项目，不限于C#（Winform\WPF），比如C++(MFC、Qt)等等。

![](https://img1.dotnet9.com/2022/05/2101.png) 

## 2. 打包使用工程

### 2.1 安装打包工具

火凤打包工具下载下来是一个压缩包文件（7.0MB），解压释放安装文件，如下图，运行安装文件：

![](https://img1.dotnet9.com/2022/05/2105.png)

先体验下打包工具的安装效果：

![](https://img1.dotnet9.com/2022/05/2106.gif) 

效果不错吧，感觉后面不用演示了...

### 2.2 打包设置

![](https://img1.dotnet9.com/2022/05/2107.png)

运行打包工具，显示无规则的打包配置界面，界面里填写软件打包的配置信息。

- 软件名称：就是您开发的项目名称，比如我写的 - “乐趣课堂”
- 软件版本：就是您的项目程序的版本号，比如我写的 - “0.1.0”
- 简短描述：您软件的描述信息，比如我写的 - “当前目标只是完成一个WPF后台管理客户端。”
- 预设风格：里面有三套免费皮肤、二套VIP皮肤，看下面录制的动图，本文选择 - 安装效果3（免费）。
- 自定义风格：VIP专享，可以配置安装程序要轮播展示的图片（5张内）、修改图标、用户许可协议。
- 高级自定义风格：VIP专享，同上，还有修改背景图片等更详细的配置。
- 选择打包目录：就是要打包的程序目录，比如我选择的- “程序目录\lqclass.com\src\LQClass.AdminForWPF\Build\publish”。
- 从打包目录选取主程序：就是程序的主程序，比如我选择的 - “程序目录\lqclass.com\src\LQClass.AdminForWPF\Build\publish\LQClass.AdminForWPF.exe”。
- 自定义扩展程序或脚本：这里比较灵活，可以为你的程序编写脚本和其他额外扩展操作，站长未测试。
- 选择安装程序保存路径：就是你打包好的exe保存在哪里。（我保存在桌面名字为“乐趣课堂.exe”）
- 其他选项见名思意：创建开始菜单项、创建桌面图标、安装完成启动主程序等
- VIP选项：站长未测试了，包年是`1888一年`...
- 定制：界面任务栏，有发布软件、定制安装、定制软件链接，要钱钱钱...

站长的配置如下：

![](https://img1.dotnet9.com/2022/05/2109.png)

下面是几种预设风格的预览图（`免费的可能不是真免费，新版全部收费了`，哭了，这里录完图还不知道...）：

安装效果1（免费）：

![](https://img1.dotnet9.com/2022/05/2107.gif) 

安装效果2（免费）：

![](https://img1.dotnet9.com/2022/05/2108.gif) 

安装效果3（免费）：

![](https://img1.dotnet9.com/2022/05/2109.gif) 

安装效果4（VIP）：

![](https://img1.dotnet9.com/2022/05/2110.gif) 

安装效果5（VIP）：

![](https://img1.dotnet9.com/2022/05/2111.gif) 

检查软件打包基本信息是否填写完整，如果OK了，进行打包操作了...

### 2.3 打包操作

点击打包工具的“一键打包”，oh，no：

![](https://img1.dotnet9.com/2022/05/2112.gif) 

本文写了一半，没想到呀，`打包也需要VIP`了，后面联系他们工作人员，给的回复：

![](https://img1.dotnet9.com/2022/05/2115.png)

录都录制了，还是完成吧，`只要你公司有钱，你本人有钱，觉得值`我也不说啥了，反正`效果不错`吧。

### 2.4 安装过程

略了，和打包工具的安装类似，没有体验卡，就不录了...

### 2.5 卸载过程

略了，和打包工具的卸载类似，没有体验卡，就不录了。

卸载打包工具是一个漂亮的橡皮擦特效卸载效果，这里还是录一下吧：

![](https://img1.dotnet9.com/2022/05/2113.gif)

## 3. 总结

打包和安装非常快，异形特效非常炫酷，`新版全部收费`了（`适合公司使用及个人产品卖的非常好，需要极致用户体验的用户`）。