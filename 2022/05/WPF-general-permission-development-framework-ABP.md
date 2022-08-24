---
title: WPF 通用权限开发框架 (ABP)
slug: WPF-general-permission-development-framework-ABP
description: 对于大部分.NET 后端开发者来说, 都比较熟悉目前流行的ABP框架, 基于开源的ABP框架, 可以自己进行二次开发, 无需重新开发一些基础功能
date: 2022-05-29 10:16:27
copyright: Reprint
author: 痕迹g
originaltitle: WPF 通用权限开发框架 (ABP)
originallink: https://www.cnblogs.com/zh7791/p/16321843.html
draft: False
cover: https://img1.dotnet9.com/2022/05/6001.png
categories: WPF,.NET课程
---

## 前言

对于大部分.NET 后端开发者来说, 都比较熟悉目前流行的ABP框架, 基于开源的ABP框架, 可以自己进行二次开发, 无需重新开发一些基础功能, 例如: 用户角色管理、权限、组织、多租户等等。

但是对于ABP框架来说, 提供给.NET开发者的可选项非常少, 目前也仅仅是提供了基于Web的解决方案, 对于桌面端以及移动设备上的解决方案, 可以说是"敷衍了事"。 哪怕是商业版的ABP, 提供桌面端和移动端的解决方案仍然只是一个简陋的架子, 对于有这方面需求的开发者, 它们只能选择不同的解决方案。

>目前大多数.NET开发者开发移动端项目多数是采用一些流行的Web解决方案, 例如: Uniapp、Electron、Flutter 等等。 由于这类的产品本身与C#就无法兼容,例如共享现有的类库, 实体、服务等。这也无法体现如今 .NET 全部一把梭的理念。

## 开发历程

考虑到目前存在许多的客户端领域开发者, 包括Xamarin.Forms开发者, 所以从2021年底开始, 我就计划着开始开发基于ABP框架的WPF实现以及Xamarin.Forms实现。

这样, 通过利用现有的技术, 实现了全平台开发的理念, 其中WPF与Xamarin.Forms项目, 实现了与后端项目共享90%以上的类库代码, 包含: 模型类、常量、接口、服务等。

截至目前为止, Xamarin.Forms与WPF还原了ABP框架 90%以上的业务功能, 包含所有的UI重新设计、业务功能实现、完整的MVVM设计。

关于Xamarin.Forms框架的实现, 参考之前的文章: [Xamarin.Forms 5.0 项目实战](https://www.cnblogs.com/zh7791/p/16079409.html)

## WPF ABP 框架介绍

本次的WPF ABP框架, 并非是通过ABP的技术手段实现了WPF项目的还原,而是基于ABP框架提供业务功能进行了完整还原, 在WPF项目当中, 移除了ABP提供的启动配置、模块系统、依赖注入及各类的反射加载、自动实体映射模等功能。

>该项目则基于大部分WPF开发者熟悉的Prism MVVM框架进行重新开发, UI则使用Syncfusion WPF版本。

该套框架包含以下功能:

- 用户和角色管理
- 组织机构
- 权限管理
- 多租户
- 本地化多语言
- 身份认证及授权
- 审计日志记录
- UI主题
- 异常处理
- 数据字典
- 系统设置

## 效果预览

- 登录页
- 包含切换租户、语言切换、修改密码、邮箱激活

![](https://img1.dotnet9.com/2022/05/6001.png)

- 首页

包含系统菜单、主题切换(深色/浅色主题)、首页数据统计面板

![](https://img1.dotnet9.com/2022/05/6002.png)

- 组织机构

维护组织信息, 添加不同的角色和用户

![](https://img1.dotnet9.com/2022/05/6003.png)

- 角色管理

维护角色信息, 设定角色权限,根据权限筛选不同的角色

![](https://img1.dotnet9.com/2022/05/6004.png)

![](https://img1.dotnet9.com/2022/05/6005.png)

- 用户管理

管理用户信息, 需改用户权限, 锁定/解锁/删除用户

![](https://img1.dotnet9.com/2022/05/6006.png)

- 审计日志

系统的请求日志、错误日志、异常数据、更改日志信息记录

![](https://img1.dotnet9.com/2022/05/6007.png)

![](https://img1.dotnet9.com/2022/05/6008.png)

- 动态属性

设置动态数据, 下拉列表、选择性、多选项等。

![](https://img1.dotnet9.com/2022/05/6009.png)

- 多租户

维护租户信息

![](https://img1.dotnet9.com/2022/05/6010.png)

![](https://img1.dotnet9.com/2022/05/6011.png)

- 版本列表

创建不同的版本,设置收费标准, 到期规则等

![](https://img1.dotnet9.com/2022/05/6012.png)

![](https://img1.dotnet9.com/2022/05/6013.png)

- 语言列表

维护多语言的数据, 修改/设定/维护相关信息

![](https://img1.dotnet9.com/2022/05/6014.png)

![](https://img1.dotnet9.com/2022/05/6015.png)

![](https://img1.dotnet9.com/2022/05/6016.png)

- 设置

包含系统的核心功能的设置, 包含租户、用户、系统安全、邮箱、发票、其它设置

![](https://img1.dotnet9.com/2022/05/6017.png)

![](https://img1.dotnet9.com/2022/05/6018.png)

![](https://img1.dotnet9.com/2022/05/6019.png)

演示UI组件

包含了一些常用的控件演示

![](https://img1.dotnet9.com/2022/05/6020.png)

- 多主题切换

![](https://img1.dotnet9.com/2022/05/6021.png)

![](https://img1.dotnet9.com/2022/05/6022.png)

![](https://img1.dotnet9.com/2022/05/6023.png)

![](https://img1.dotnet9.com/2022/05/6024.png)

![](https://img1.dotnet9.com/2022/05/6025.png)

## 如何获取源代码？

参与了上次的Xamarin.Forms公益活动的同学, 可以单独与本人取得联系, 可以免费获取 WPF版本完整的项目源代码, 针对Xamarin.Forms以及WPF的ABP框架, 会在后续持续进行优化, 且获得免费的技术相关咨询服务。关于上次的Xamarin.Forms公益活动产生的所有收益, 会在近期的捐赠活动进行公示。

针对未参与上次公益活动以及想要获取源代码或者进行商业性质的二次开发人员, WPF版本的ABP框架完整源代码费用:499元，可以单独与作者(QQ:779149549)取得联系获取。

>本次WPF框架如收益超过3W的部分, 将同样以公益活动的形式进行捐赠, 关于未来的MAUI框架版本, 会在接下来进行移植工作。

## 视频教程说明

WPF版本的项目持续优化的过程中, 同样会陆续制作相关教程发布在视频平台中, 大家可以持续关注。