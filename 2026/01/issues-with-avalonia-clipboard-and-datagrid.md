---
title: Avalonia剪贴板和DataGrid的问题
slug: issues-with-avalonia-clipboard-and-datagrid
description: 记录最近 Avalonia 桌面软件开发解决的两个问题：剪贴板复制崩溃、Tab 切换 DataGrid 卡顿，分析根因并给出解决方案
date: 2026-01-11 11:18:01
lastmod: 2026-01-11 12:23:41
cover: https://img1.dotnet9.com/2026/01/cover_03.png
banner: false
categories:
  - .NET
  - Avalonia
---

![](https://img1.dotnet9.com/2026/01/cover_03.png)

记录下最近基于 Avalonia 开发桌面应用时遇到并解决的两个典型问题，希望能给遇到同类问题的开发者提供参考。

## 1. 剪贴板复制异常（AOT 崩溃 / 调试抛 COM 异常）

### 问题现象
项目中对`TextBox`或`SelectedTextBlock`控件的文本执行Ctrl+C复制操作时，出现两种异常表现：

- AOT发布运行程序直接崩溃，查看应用程序事件：

![](https://img1.dotnet9.com/2026/01/0301.png)

- Debug调试模式下，未捕获全局异常，`Program`的`Main`函数会抛出明确的COM异常：

```shell
System.Runtime.InteropServices.COMException:“尚未调用 CoInitialize。 (0x800401F0 (CO_E_NOTINITIALIZED))”
```

### 根因定位

排查代码后发现核心问题出在`Program.cs`的`Main`函数定义上——将入口函数声明为`async Task`类型，而非Avalonia要求的同步：

![](https://img1.dotnet9.com/2026/01/0302.png)

将**async Task**改为**void**修复，Avalonia[文档]([Application Lifetimes | Avalonia Docs](https://docs.avaloniaui.net/docs/concepts/application-lifetimes))其实有说明：

![](https://img1.dotnet9.com/2026/01/0303.png)

### 原理分析
Avalonia作为桌面UI框架，对主线程的`SynchronizationContext`（同步上下文）有强依赖，剪贴板、窗口消息循环等系统级操作均需基于稳定的主线程上下文执行。
- `async Task Main`会让CLR以异步方式管理主线程生命周期，破坏Avalonia初始化的同步上下文，导致剪贴板操作时触发`CoInitialize`未调用的COM异常；
- Avalonia官方文档明确说明：应用入口阶段（Main函数）不应依赖未就绪的`SynchronizationContext`，因此入口函数必须为同步。

## 2. Tab切换DataGrid卡顿

### 问题场景

项目使用[Dock](https://github.com/wieslawsoltes/Dock)控件（VS风格布局，Document等价于TabControl的TabItem），在Document中嵌入`DataGrid`展示数据：

- 数据量极小（仅几十条、8-9个字段）；
- 运行环境为Win7/Windows Server 2019，Win10同事反馈也卡；
- 现象：Debug模式切换Tab卡顿2秒左右，AOT发布后卡顿加剧至3-4秒；
- 对比：单窗口直接展示`DataGrid`加载几十万条数据（15个字段）时，实时刷新很流畅。

### 排查过程
1. 初期怀疑`DataGrid`使用方式问题：优化列绑定（仅单向绑定）、移除复杂样式/动画，卡顿无明显改善；
2. 怀疑Dock控件性能问题：更新Dock控件版本、切换旧版本，问题依旧；
3. 排除Dock控件影响：改用原生`TabControl`测试，卡顿问题复现；
4. 社区求助：在Avalonia交流群反馈后，群友提示尝试`TreeDataGrid`替代`DataGrid`，替换后卡顿问题完全解决。

`DataGrid`是早期移植的WPF（链接指Silverlight）风格组件，“Cell级控件+被动虚拟化”的设计在小数据量+Tab切换重建时，控件实例化/布局开销被Win7/AOT环境放大；而`TreeDataGrid`是Avalonia团队重构的新组件，“行级渲染+强制虚拟化”的设计从根源上降低了切换开销，且官方已明确推荐使用`TreeDataGrid`替代`DataGrid`,讨论请点击[链接](https://github.com/AvaloniaUI/Avalonia/discussions/18388)。

![](https://img1.dotnet9.com/2026/01/0304.png)

这里要明确，**TreeDataGrid**已被Avalonia放入[商用套件](https://docs.avaloniaui.net/zh-Hans/docs/reference/controls/treedatagrid/treedatagrid-column-types)中

![](https://img1.dotnet9.com/2026/01/0305.png)

使用免费版，必然会有新发现Bug得不到修复的情况（官方把旧版维护推给了开源社区分支），**Avalonia.Controls.TreeDataGrid**免费版可使用[11.1.1](https://www.nuget.org/packages/Avalonia.Controls.TreeDataGrid/11.1.1)及以前版本：

![](https://img1.dotnet9.com/2026/01/0306.png)

Semi提供的配套主题库对应版本为[11.1.1.1](https://www.nuget.org/packages/Semi.Avalonia.TreeDataGrid/11.1.1.1)

## 总结
1. Avalonia入口函数`Main`必须声明为同步，`async Task`会破坏同步上下文，导致剪贴板等系统级操作异常；
2. `DataGrid`在Tab切换场景下存在性能短板，优先使用官方推荐的`TreeDataGrid`可解决卡顿问题；
3. Avalonia的老组件（如DataGrid）在老系统（Win7/Server）+AOT发布环境下易暴露性能问题，优先选择官方新组件可减少踩坑，切记：及时更新最新控件库。

最后感谢Avalonia交流群的群友提供的解决方案，开源社区的交流总能快速定位这类“反直觉”的问题~

