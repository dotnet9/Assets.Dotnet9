---
title: Avalonia剪贴板和DataGrid的问题
slug: issues-with-avalonia-clipboard-and-datagrid
description: 记录最近Avalonia桌面软件开发解决的两个问题
date: 2026-01-11 11:18:01
lastmod: 2026-01-05 19:47:21
cover: https://img1.dotnet9.com/2026/01/cover_03.png
banner: true
categories:
  - .NET
  - Avalonia
---

记录下最近解决的两个问题：

1. 剪贴板复制异常
2. Tab切换DataGrid卡顿

## 1. 剪贴板复制异常

AOT发布后，对**TextBox**或**SelectedTextBlock**控件选择文本复制程序必崩溃，通过查看系统的应用程序事件得到异常日志如下：

![](https://img1.dotnet9.com/2026/01/0301.png)

调试状态，如果未捕获全局异常，则Program的Main函数会抛出异常：

```shell
System.Runtime.InteropServices.COMException:“尚未调用 CoInitialize。 (0x800401F0 (CO_E_NOTINITIALIZED))”
```

最终检查代码是这个原因：

![](https://img1.dotnet9.com/2026/01/0302.png)

将**async Task**改为**void**修复，Avalonia[文档]([Application Lifetimes | Avalonia Docs](https://docs.avaloniaui.net/docs/concepts/application-lifetimes))其实有说明：

![](https://img1.dotnet9.com/2026/01/0303.png)

中文意思：入口点。目前尚未准备好，因此在此阶段不应使用任何 Avalonia 类型或依赖于 SynchronizationContext 已就绪的组件

**总结：多翻看文档没坏处**

## 2. Tab切换DataGrid卡顿

**场景：**使用[Dock]([wieslawsoltes/Dock: A docking layout system.](https://github.com/wieslawsoltes/Dock))控件（类似VS中的Dock布局），Document（类似TabControl的TabItem）中使用DataGrid加载数据，数据只有几十条。

**问题：**调试时有明显卡顿（2秒左右，Win7或Windows Server 2019），AOT运行更甚达到3、4秒

1. 一再检查DataGrid是不是使用有问题，显示使用列单向绑定（只是展示使用），列复杂效果去除，效果甚微。
2. 怀疑Dock控件有性能问题，问群，更新最新版、切换旧版，依然未得到解决。
3. 可能不是Dock问题，改用TabControl控件，问题依旧。
4. 写了Demo，抛到Avalonia交流群，群友说StackPanel布局导致DataGrid UI虚拟化失效，试了也不行，最后群友给了TreeDataGrid代码，尝试解决了，还是万能的群友牛逼，感谢。

上一个项目使用DataGrid加载1、20万条数据（15个字段）实时刷新都不带卡的，但场景是在一个窗口展示，Tab切换卡这个没细究，Avalonia也是建议使用[TreeDataGrid]([TreeDataGrid 树状数据表格 | Avalonia Docs](https://docs.avaloniaui.net/zh-Hans/docs/reference/controls/treedatagrid/))，讨论请点击[链接]([Announcement: Avalonia DataGrid is Moving to a New Home · AvaloniaUI/Avalonia · Discussion #18388](https://github.com/AvaloniaUI/Avalonia/discussions/18388))：

![](https://img1.dotnet9.com/2026/01/0304.png)

