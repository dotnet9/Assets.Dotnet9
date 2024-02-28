---
title: WPF混合Blazor做个简易聊天小程序
slug: wpf-with-blazor-chat-test
description: 晚上花了4、5个小时，学习了下Wpf + Blazor混合模式开发，感觉不错
date: 2022-10-28 01:09:47
copyright: Original
author: 沙漠尽头的狼
draft: false
cover: https://img1.dotnet9.com/2022/10/2-chat-window.png
categories: .NET
tags: .NET,WPF,Blazor
---

大家好，我是沙漠尽头的狼。

今天尝试了下WPF混合Blazor开发，感觉不错，顺便把测试的程序简单分享下：WPF混合Blazor开发的一个简易对话程序。

使用技术栈：

- [.NET 7](https://learn.microsoft.com/zh-cn/aspnet/core/blazor/hybrid/tutorials/wpf?view=aspnetcore-7.0)
- [Prism 8](https://github.com/PrismLibrary/Prism)
- [Masa Blazor](https://blazor.masastack.com/)

## 搭建WPF+Blazor程序

学习WPF + Blazor混合开发的`Hello World`最好的地方是微软文档：

https://learn.microsoft.com/zh-cn/aspnet/core/blazor/hybrid/tutorials/wpf?view=aspnetcore-7.0

## 效果

UI使用了[Masa Blazor](https://blazor.masastack.com/)，效果个人感觉不错，如果用WPF实现，要麻烦不少，以下是几个效果截图：

**用户列表窗口**

使用了[Masa Blazor](https://blazor.masastack.com/)的列表组件，代码几乎是直接Copy过来的，参考链接[Masa Blazor列表](https://blazor.masastack.com/components/lists)：



![用户列表](https://img1.dotnet9.com/2022/10/1-main-window.png)

**聊天窗口**

这个简单，左侧是一个列表，同上面的用户列表类似，只是去掉了上方蓝色的`MToolbar`和用户的详细描述信息，右侧则是多行文本框显示聊天记录、单行文本框输入即时聊天信息、一个发送按钮（简单描述，不贴代码，后面有仓库链接）。

![聊天窗口](https://img1.dotnet9.com/2022/10/2-chat-window.png)

**打开子窗口**

列表的点击事件，使用`IEventAggregator`发送打开子窗体事件 `OpenUserDialogEvent`，事件订阅方法执行弹出子窗体操作：

![打开窗口](https://img1.dotnet9.com/2022/10/3-open-child-window.gif)

**演示发送消息**

发送消息按钮点击，使用`IEventAggregator` 发送发送消息事件`SendMessageEvent`，事件订阅方法接收消息，并追加到各自历史聊天多行文本框展示：

![演示发送消息](https://img1.dotnet9.com/2022/10/4-send-message.gif)

## 源码

Github：https://github.com/dotnet9/WPFBlazorChat

效果还行，代码就不解释了，有兴趣的跑起来看看，目前有几点后面有时间再优化，毕竟现在快凌晨两点了：

- 自定义的窗体还是WPF模式实现的

  窗体透明，Border鼠标按下事件实现窗体拖动、右上角关闭窗体按钮实现窗体关闭，后面有空再尝试也使用Razor实现吧。

- Prism.DryIoc和IServiceCollection两个Ioc容器重复注册对象

  本以为搞混合开发挺简单的，实际做才会遇到问题，如果要实现模块化，两种容器可能会处理类似的对象依赖注入，比如`IEventAggregator`在`Prism`中是默认注入了，如果`Razor`中使用还要注入到`IServiceCollection`中。

更多点，后面再补充，今天只是尝试...