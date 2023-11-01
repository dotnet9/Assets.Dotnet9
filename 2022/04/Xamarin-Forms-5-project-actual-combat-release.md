---
title: Xamarin.Forms 5.0 项目实战发布！
slug: Xamarin-Forms-5-project-actual-combat-release
description: 本次活动主要是 .NET Xamarin.Forms 移动端项目开发实战教程
date: 2022-04-13 19:49:17
copyright: Reprinted
author: 痕迹g
originaltitle: Xamarin.Forms 5.0 项目实战发布！
originallink: https://www.cnblogs.com/zh7791/p/16079409.html
draft: False
cover: https://img1.dotnet9.com/2022/04/1305.png
categories: .NET课程
---

## 活动介绍

本次活动主要是 .NET Xamarin.Forms 移动端项目开发实战教程, 与以往相同, 本次的收入(关于其它部分会另行说明)也将用于社区公益活动, 不限于:

- 公益性质的个人/组织机构捐赠
- 开源社区个人/项目捐赠
- 内部投票活动

本次的活动费用为:399元, 相对于去年组织的WPF公益实战视频而言, 这次的内容除了是针对Xamarin.Forms以外, 整体的内容几乎是针对常见的商业化需求进行开发。包含以下:

- 后端 (ASP.NET Core WebApi) 与商业版一致
- 前端 (Angular) 与商业版一致
- 移动端 (Xamarin.Forms) 重构可商用
- 部署文档以及项目解决方案文档说明
- Xamarin.Forms 项目文档

包含完整的后端+前端+移动端源代码, 移动端源代码完全重构可商业化使用。

>关于基于ABP的完整WPF版本, 则会在本次Xamarin.Forms 发布之后进行开发(实际上,去年年尾已经开发了发部分), 会在这两个月发布, 随后投入ABP框架移植MAUI的开发与教程制作工作。

以往的组织活动, 详见:

- [WPF-Prism8.0核心教程(公益)](https://www.cnblogs.com/zh7791/p/14140920.html) - [点击观看视频](https://www.bilibili.com/video/BV1Ei4y1F7du?spm_id_from=333.999.0.0)

- [2022年终结版WPF项目实战](https://www.cnblogs.com/zh7791/p/15754663.html) - [点击观看视频](https://www.bilibili.com/video/BV1n3411x7VW?spm_id_from=333.999.0.0)

## 如何参与活动?

参与活动请加入QQ群:

- QQ群:864083645
- 群答案: 微软系列技术教程

**特别说明:**

1. 关于之前参与过《2022终结版WPF项目实战》或《ABP框架活动》的朋友可减去已支付的99元, 这部分不包含捐赠范围内。
2. 关于视频部分, 会统一上传至B站平台进行观看, 所有源代码/文档如有更新, 会在群内进行统一通知。
3. ABP商业版目前的版本是11.1.0 (2022-02-28), 支持到2022-09-15为止, 中间有任何新版本发布, 如有需要可以与本人联系。

## 项目介绍

本次项目实战是基于商业版的ABP进行二次开发, 在不破坏原有的基础设施的情况下, 针对移动端Xamarin.Forms进行完全重构, 移除了ABP提供的各种依赖组件，使用主流的开源框架进行
重新开发, 其中包括不限于: Pirms.Forms、Syncfusion、XamarinCommunityToolkit、Xam.Plugin、ArcUserDialogs等等。

项目主要分为三个部分：

- 后端(ASP.NET Core WebApi) :提供AB中业务功能的Web服务
- 前端(Angular) : 集成ABP中所有功能的Web网页
- 移动端(Xamarin.Forms) : 集成ABP中所有功能运行在Android与iOS设备上的原生APP

关于后端以及前端部分会在视频以及开发文档当中介绍, 那么下面, 会主要来介绍本次发布的Xamarin.Forms框架的内容。

功能主要包含如下:

- 系统登录/注销/找回密码/发送邮件
- 用户模块
- 角色模块
- 组织结构模块
- 多租户模块
- 语言模块
- 版本管理
- 动态属性
- 审计日志
- 系统设置
- 个人信息
- 主题设置

关于Xamarin.Forms部分, 是完成进行重构开发, 可以进行商业化使用, 但其依赖的UI组件则需要符合其使用条件(这点会在开发文档中说明)。

对于后端的Web服务由于是使用商业版进行二次开发，故不能进行商业化部署, 仅适用于学习目的。如果需要进行商业化开发, 请单独联系本人。

>ABP后端以及前端部分未经过修改, 兼容ABP的所有官方文档, 包括使用其代码生成器等功能。

## 学习路线

本次的项目，主要是通过项目实战的方式教大家如何使用Xamarin.Forms进行实际开发，其中包含常见的开发需求, 如下:

- 授权登录注销
- 本地化以及多语言切换
- 支持多种系统主题
- 容器以及依赖注入
- MVVM框架使用
- 实体映射及验证
- 常见布局以及UI组件
- 发布订阅组件
- Web服务
- 异常处理

关于具体的内容, 会在开发文档中给大家详细介绍, 以及包含Xamarin.Forms本身的内容, 文档大致如下:

![](https://img1.dotnet9.com/2022/04/1301.png)

## Xamarin.Forms 效果图

下面主要是本次项目当中的一些实际运行的项目部分截图(包含iOS以及Android):

### 安卓子系统

登录页

![](https://img1.dotnet9.com/2022/04/1302.png)

首页

![](https://img1.dotnet9.com/2022/04/1303.png)

主题

![](https://img1.dotnet9.com/2022/04/1304.png)

系统菜单

![](https://img1.dotnet9.com/2022/04/1305.png)

用户管理

![](https://img1.dotnet9.com/2022/04/1306.png)

审计日志

![](https://img1.dotnet9.com/2022/04/1307.png)

语言管理

![](https://img1.dotnet9.com/2022/04/1308.png)

新建租户

![](https://img1.dotnet9.com/2022/04/1309.png)

### iOS设备

登录

![](https://img1.dotnet9.com/2022/04/1310.jpg)

首页

![](https://img1.dotnet9.com/2022/04/1311.jpg)

新建用户

![](https://img1.dotnet9.com/2022/04/1312.jpg)

语言列表

![](https://img1.dotnet9.com/2022/04/1313.jpg)

审计日志

![](https://img1.dotnet9.com/2022/04/1314.jpg)

......

## 结尾

在最后，也给大家分析一下如今国内.NET开发为什么很少使用Xamarin.Forms的原因以及为什么我要做类似的事情。

### Xamarin.Forms为什么很少人使用？

主要的原因可能就是以下几点:

- 视频教程稀缺，微软的官方文档做的很好但也无法形成一个完整学习体系。
- 国内主流的.NET开发者基本上不用Xamarin.Forms，大部分只是追随市场用一些Web技术跨平台开发。(这与大部分从业者有关 BS行业的工作者)
- 大量的.NET客户端开发者仍然不知道.NET可以进行移动端开发
- 国内开源的案例、相关组件几乎没有，大部分仍然是以国外为主。

### 这么少人用, 为什么你还选择它？

事实上，从我接触WPF开始，网络上WPF的教学资源就几乎没有，微软官方文档也是敷衍了事，相关的学习案例就更加不用想。
而确定一点的是，WPF国内的市场比Xamarin多的多，所以Xamarin更加不用想象会有多惨。

从2019年开始, 我在网络上陆续发布WPF、Xamarin、ASP.NET Core相关教学视频，整体性来讲, 除了ASP.NET Core相关的内容网络上确实是相对多,属于一个资源相对.NET领域饱和的这么一个状态，那么对于WPF以及Xamarin.Forms而言, 现阶段已经在国内的大部分平台搜索, 我的内容已经出现在最前面了 (无论是B站、抖音、今日头条还是西瓜视频)。

这也恰恰说明了在这方面做的人少的表现，所以今年会在客户端领域加大力度 (WPF/Xamarin/MAUI), 以及推出更多的项目实战案例来给大家学习以及参考使用。