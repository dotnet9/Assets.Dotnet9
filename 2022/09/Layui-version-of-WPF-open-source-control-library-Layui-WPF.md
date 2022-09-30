---
title: Layui版本的WPF开源控件库-Layui-WPF
slug: Layui-version-of-WPF-open-source-control-library-Layui-WPF
description: 一个WPF版的Layui前端UI样式库
date: 2022-09-30 21:07:47
copyright: Default
author: 沙漠尽头的狼
draft: false
cover: https://img1.dotnet9.com/2022/09/layui-wpf-cover.png
categories: WPF
albums: 开源WPF
---

大家好，我是沙漠尽头的狼。

今天介绍一款Layui风格的WPF开源控件库，仓库信息如下：

仓库地址：https://github.com/Layui-WPF-Team/Layui-WPF

仓库截图：

![Layui-WPF](https://img1.dotnet9.com/2022/09/repository-layui-wpf.png)

关于Layui请点击此[链接](https://layuion.com/)了解，本文不做介绍，下面我们从控件源码及已有的控件截图进行欣赏。

## 控件源码

克隆控件仓库：

```shell
git clone https://github.com/Layui-WPF-Team/Layui-WPF.git
```

使用VS打开，控件解决方案如下：

![控件源码结构](https://img1.dotnet9.com/2022/09/layui-wpf-in-vs.png)

看了几个工程目标框架默认是 .NET Framework 4.5.2，兼容大部分平台了，有其他.NET版本需求的可以自己编译尝试。

另解决方案中引用了log4net库做为日志记录组件，Prism做为MVVM框架，解决方案直接编译没有错误：

![成功编译](https://img1.dotnet9.com/2022/09/layui-wpf-compile-success.png)


将`LayuiApp`工程做为启动项目，成功运行，下面对部分控件进行截图预览。

## 控件效果预览

### 基本元素

- 按钮

![按钮](https://img1.dotnet9.com/2022/09/button-of-layui-wpf.png)

- 表单

![按钮](https://img1.dotnet9.com/2022/09/form-of-layui-wpf.png)

- 选项卡

![选项卡](https://img1.dotnet9.com/2022/09/tab-of-layui-wpf.gif)

- 进度条

![进度条](https://img1.dotnet9.com/2022/09/progress-of-layui-wpf.gif)

- 面板

![面板](https://img1.dotnet9.com/2022/09/progress-of-layui-wpf.gif)

- 折叠面板

![面板](https://img1.dotnet9.com/2022/09/folding-board-of-layui-wpf.gif)

- 过渡动画

![过渡动画](https://img1.dotnet9.com/2022/09/Transition-animation-of-layui-wpf.gif)

- 加载动画

![加载动画](https://img1.dotnet9.com/2022/09/loading-animation-of-layui-wpf.gif)

- Gif动画

支持网络gif文件和本地gif文件

![Fif动画](https://img1.dotnet9.com/2022/09/gif-animation-of-layui-wpf.gif)

- 时间线

![时间线](https://img1.dotnet9.com/2022/09/timeline-of-layui-wpf.png)

- 辅助线

![辅助线](https://img1.dotnet9.com/2022/09/help-line-of-layui-wpf.png)

### 组件示例

- ToolTip

![ToolTip](https://img1.dotnet9.com/2022/09/tooltip-of-layui-wpf.gif)

- 标记

![标记](https://img1.dotnet9.com/2022/09/tip-button-of-layui-wpf.png)

- 弹出框

![弹出框](https://img1.dotnet9.com/2022/09/popup-of-layui-wpf.gif)

- 抽屉

![抽屉](https://img1.dotnet9.com/2022/09/drawer-of-layui-wpf.gif)

- 表格

展示有表头合并效果：

![表格](https://img1.dotnet9.com/2022/09/table-of-layui-wpf.gif)

- 键盘

有几种配色风格

![键盘](https://img1.dotnet9.com/2022/09/keyboard-of-layui-wpf.gif)

最后来个控件菜单切换结束控件预览：

![菜单切换](https://img1.dotnet9.com/2022/09/menu-change-of-layui-wpf.gif)

## 结束

最后再给上仓库链接，有兴趣去克隆研究吧，看仓库最后一次提交记录是22小时前，作者（3个参与人员）还在积极的更新迭代中：

- Layui-WPF：https://github.com/Layui-WPF-Team/Layui-WPF