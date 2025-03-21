---
title: C#开源项目：SiMay远程控制管理系统
slug: csharp-open-source-project-simay-remote-control-management-system
description: 一个底层基于IOCP异步通信模型的Windows远程控制系统
date: 2020-12-04 08:33:55
copyright: Reprinted
author: dWwwang
originalTitle: C#开源项目：SiMay远程控制管理系统
originalLink: https://gitee.com/dotnetchina/SiMayRemoteMonitorOS
draft: False
cover: https://img1.dotnet9.com/2020/12/cover_02.jpg
albums:
    - Winform开源项目
categories: 
    - Winform
tags: 
    - Winform
    - SiMay
    - 开源Winform
    - 远程控制
    - IOCP
    - 异步通信
---

> 本项目是一个 Windows 远程控制系统，项目完全采用 C#.NET 开发，实现了基于逐行扫描算法远程桌面，桌面视图墙，文件管理，实时语音、视频监控，注册表管理，实时进程管理等功能，各模块采用独立连接，支持异常情况重连。实现了中间会话服务器，支持多主控端同时监控，支持 Web 端，欢迎点 Start 关注，项目不定时更新，源代码仅供参考，不得用于非法用途，否则一切后果自负。

Gitee 仓库截图

![Gitee仓库截图](https://img1.dotnet9.com/2020/12/0201.png)

下方基于原项目仓库 readme

## 系统介绍

- SiMay 远程控制管理系统是一个 Windows 远程控制系统，底层基于 IOCP 的异步通信模型，能对海量客户端实时监控，目前功能已实现：逐行扫描远程桌面经典的文件管理、实时远程语音、实时摄像头、经典注册表管理、命令行终端、实时系统进程管理、用户桌面视图墙轮播等功能。并且可捕获 UAC,WinLogon 桌面。系统实现了中间会话服务器，可支持不同平台多主控端同时监控同一被控端。被控服务端支持绿色启动及以系统服务方式安装，项目完全采用 C#.NET 开发，代码仅供参考，项目不定时更新，欢迎关注点星星，fork。欢迎入群技术交流:905958449 :laughing: :blush:

## 企业功能定制项

- 远程桌面升级(综合性能提升 50%、带宽占用更小)、Web 版远程桌面、(摄像头、语音、屏幕广播)、文件分发、支持远程服务器桌面等功能，欢迎进群咨询群主。

## 申明

- 作为创作者，我对由此软件引起的任何行为和/或损害不承担任何责任。 您对自己的行为承担全部责任，并承认此软件仅用于教育和研究目的。 不得用于您不拥有或有权使用的任何系统。 使用此软件，您自动同意上述内容，感谢支持。

## 背景

- 本项目仅为个人项目，经过几次重构，系统相对比较成熟了，决定开源反馈开源社区，希望更多人能和我一起进步，欢迎吐槽改进。

### 主控界面

![主控界面](https://img1.dotnet9.com/2020/12/0202.jpeg "主控制界面")

### 创建服务端

![创建服务端](https://img1.dotnet9.com/2020/12/0203.png "创建服务")

### 远程桌面

![远程桌面](https://img1.dotnet9.com/2020/12/0204.jpeg "远程桌面")

### 文件管理

![文件管理](https://img1.dotnet9.com/2020/12/0205.jpeg "文件管理")

### 语音传输

![语音传输](https://img1.dotnet9.com/2020/12/0206.jpeg "语音传输")

### 注册表管理

![注册表管理](https://img1.dotnet9.com/2020/12/0207.jpeg "注册表管理")

### 中间服务器

![中间服务器](https://img1.dotnet9.com/2020/12/0208.png "多对一实时控制")

## 系统项目结构

### SiMay.Core【公共核心功能】

1.  SiMay.Basic --基础通用库
2.  SiMay.Core.Standard --系统核心统一公共库【统一通讯指令丶共用组件丶通信数据实体等..】
3.  SiMay.Serialize.Standard --轻量级高性能二进制序列化库【作用:系统通信数据实体化】
4.  SiMay.ModelBinder --调用绑定器

### SiMay.RemoteMonitor【主控制端】

1.  SiMay.RemoteControls.Core --主控端核心库
2.  SiMay.RemoteMonitor.Windows --Windows 主控管理端
3.  SiMay.RemoteMonitor.Web --Web 主控端
4.  SiMay.RemoteMonitorForWebSite --Web 监控前端

### SiMay.Platform【平台实现】

1.  SiMay.Platform.Windows -- 基于 Windows 的功能实现

### SiMay.RemoteService【远程被控服务端】

1.  SiMay.RemoteService.Loader --内存加载 Loader，实现远程内存载入被控端核心库
2.  SiMay.ServiceCore --被控端核心库

### SiMay.SessionProvider【会话提供层】

1.  SiMay.Net.SessionProvider --会话提供库【作用：提供服务器监听模式或者中间会话代理协议】
2.  SiMay.Net.SessionProvider.Core --代理协议统一公用库【作用：统一中间库和服务器的通信指令及序列化等】
3.  SiMay.Net.SessionProviderServiceCore -- 中间服务核心库
4.  SiMay.Net.SessionProviderService --中间会话代理服务器【作用：提供保持服务端会话保持丶数据转发功能，基于此实现多平台端监控】

### SiMay.Sockets【通信层】

1.  SiMay.Socket.Standard --轻量级通信引擎
2.  SiMaySocketTestApp --通信引擎测试程序

## 编译

Bin 为编译目录，重新生成后，主控程序将编译到此目录，Bin->dat 目录为被控服务端目录，被控服务端编译后在此。(没有目录新建一下)

## 运行

1.  局域网

主控端:打开位与 Bin 目录下的主控端程序 SiMayRemoteMonitor.exe，确认系统设置服务器地址为 0.0.0.0(监听本机所有网卡)，端口默认 5200，使用会话模式为=本地服务器，然后保存配置重启程序,
重启后日志输出监听成功，即主控端设置正确。

被控服务端创建:打开主控端-->创建客户-->地址输入本机物理地址(或 127.0.0.1)，端口设置为服务端监听端口(默认 5200)-->点击连接测试检查配置是否正确-->创建服务端文件，服务端文件即为配置完成的被控端程序(如提示找不到文件，请检查被控服务程序是否存在[编译步骤是否正确])，双击运行被控服务程序即可在主控端看见服务在线信息，如主控端无在线信息，请检查上述步骤是否配置正确。

2.  广域网

条件:需要主控端处于公网环境(或者设置路由内网映射、使用内网映射工具[如花生壳，内网通])，并且开放主控端监听端口(注意检查端口是否开放、防火墙通行规则)。
创建客户端-->被控服务端连接至主控端的公网地址，端口即可

3.  中间服务器部署

条件:需要中间服务器处于公网环境(建议部署在公网服务器，或者设置路由内网映射)，并且开放中间服务器监听端口(默认 522 端口、注意检查端口是否开放、防火墙通行规则)。

主控端设置: 系统设置-->会话服务器地址 输入 中间服务器的公网地址，端口。-->设置会话模式为:中间会话模式-->确认 AccessKey 与中间服务器 Accesskey 一致。(中间会话服务器系统设置位于标题栏系统菜单右键)-->创建客户端并选择会话模式为中间会话模式，ip，端输入中间服务器的公网地址即可

4.  Web 端监控

编译 SiMay.RemoteMonitor.Web.exe，Web 服务为控制台形式无系统设置界面，可直接使用 Windows 控制端保存的系统配置文件 SiMayConfig.ini，会话模式可使用服务器模式或者中间会话模式启动，启动成功后控制台打印监听成功或初始化成功字样即设置正确(服务器模式为监听成功，中间会话模式为初始化成功 及 WebSocket 端口监听成功)，如有被控端连接成功，控制台会实时打印上线连接信息，Web 服务设置完成。

上述 Web 服务设置完成后，下一步需要部署 Web 网站 SiMay.WebRemoteMonitor，首先打开 Index.html 文件编辑 WebSocket 连接地址，指向 Web 服务的公网地址与端口即可。

使用浏览器，访问 SiMay.WebRemoteMonitor 网站，页面弹出 Id，Key 输入框即表示与 Web 服务连接成功，输入 SiMay.RemoteMonitorFor.Web.exe 配置的账号密码即可登录，连接成功后页面可看到被控服务端计算机桌面视图，长按视图可打开更多功能。

## 技术

1.  组件式系统架构设计
2.  远程同步调用
3.  实体消息传输协议
4.  应用多连接会话支持
5.  可视区域逐行扫描算法的远程桌面
6.  中间会话服务转发，支持多个主控端同时实时监控
7.  HOOK 技术
8.  WebSocket Web 端监控
9.  IOCP 异步 Socket 高性能通信模型
10. 基于 Windows WaInXX 系列实现的语音通讯
11. 基于 Dx 组件捕获摄像头

## 开发环境

1.  建议 Visual Studio 2019 企业版

## 参与贡献

1.  Fork 本仓库
2.  新建 Feat_xxx 分支
3.  提交代码
4.  新建 Pull Request

## 未来构想

1.  移动 Web 监控端
2.  跨平台的系统管理监控

> - 仓库地址：[SiMay 远程控制管理系统](https://gitee.com/dotnetchina/SiMayRemoteMonitorOS)
