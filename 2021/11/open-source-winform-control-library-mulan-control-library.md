---
title: 开源Winform控件库：花木兰控件库
slug: open-source-winform-control-library-mulan-control-library
description: 可以编译该项目。整个控件控除了动画函数由Silverlight提取出来和ColorEditorExt.cs颜色面板视图设计器扩展器在网上例子修改而来，其他都是自己在原生控件基础上写的，没有使用任何第三方库，所以放心使用，没有侵犯他人著作权的问题。
date: 2021-11-21 19:48:23
banner: false
copyright: Original
originalTitle: 开源Winform控件库：花木兰控件库
draft: False
cover: https://img1.dotnet9.com/2021/11/cover_07.gif
albums:
    - Winform控件库
categories: 
    - Winform
tags: 
    - C#
    - Winform
    - 开源Winform
---

微信好友推荐，挺好看的 Winfrom 控件库，下面来看看。

![花木兰控件库Gitee截图](https://img1.dotnet9.com/2021/11/0720.png)

## 介绍

- 基于 **C#（语言） 4.0** 、 **VS2019** 、 **Net Framework 4.0(不包括 Net Framework 4.0 Client Profile)** 开发的 Winform 控件库。为了兼容性采用了 C#（语言） 4.0 版本，低版本 VS 也可以编译该项目。整个控件控除了动画函数由 Silverlight 提取出来和 ColorEditorExt.cs 颜色面板视图设计器扩展器在网上例子修改而来，其他都是自己在原生控件基础上写的，没有使用任何第三方库，所以放心使用，没有侵犯他人著作权的问题。

- 这套控件库原本在博客上都是单个控件发布的，这次在 gitee 整体的发布。由于原来控件都是独立开发，大量的控件使用到滑动的效果，导致定时器消耗过多，所以在整体发布前对大部分控件做了修改，不排除还有 bug，所以这套控件库适合有基本基础控件开发的人使用。控件本身并不复杂，像 window 消息使用的比较小，主要都是重写 Paint 方法实现。还有就是所有的控件目前都是采用整体刷新方式绘制，你可以继续优化控件。这些控件都是我平常出于好奇心写的，没有在真正的项目上使用过，你要是使用在自己的项目中，最好先测试下控件有没有 bug，为什么这么说呢，因为我在开发这些控件时就有遇到过控件有 bug 导致在操作视图设计器时 VS 奔溃自动关闭的现象。开发可化视图设计器的控件还是挺麻烦的，你必须要了解 VS 视图设计器的流程原理。

## 关于授权

- 关于授权问题有以下 **3 种** 方式：（以下都不提供 BUG 解决服务，我也没有刻意留下 bug）

  1.  **30 元** (人民币)永久授权(适用以后所有版本)，控件库可以集成在你的商业系统中使用，但控件库不能用于二次贩售和授权他人，对于二次开发看下面 2 的情况。

  2.  **免费** 永久授权(适用以后所有版本)，你可以用于学习但禁止任何商用。但是如果你在这些控件的基础上进行二次开发，当你的控件库的功能都比我免费授权的源码功能强大一倍后还有代码相似度在一半以下，你可以独立发布贩售你的源码，但要在你的源码版权上加上一句描述“该控件库是以花木兰控件库为基础开发而来的”，如果你的二次开发导致你的控件库源码和我免费授权的源码有 90%的非相似度就可以不用加刚才说的那句描述，因为我承认一个成功的借鉴就是原创。

  3.  **免费** 永久授权(适用以后所有版本)，可以免费让控件库集成在你的商业系统中使用，但控件库不能用于二次贩售和授权他人。还有你的系统中要用到该控件库的文件都要加上我的版权描述，特别是木兰诗不能删掉，不要问为什么。

## 仓库介绍

从 Gitee 仓库[花木兰控件库](https://gitee.com/tlmbem/hml.git)克隆下来后，下面是仓库总体目录结构：

![花木兰控件库目录](https://img1.dotnet9.com/2021/11/0701.png)

作者解决方案用的中文，嗯，没毛病。

解决方案用[Microsoft Visual Studio Enterprise 2022 (64-bit) - Preview]打开，为啥用 VS 2022 预览版，不是正式版已经出来了吗？(...)

解决方案结构：

![解决方案结构](https://img1.dotnet9.com/2021/11/0702.png)

我们不看源码，你有兴趣可以研究，选择`WinfromDemo`工程做为启动项目，F5 运行，来介绍几个效果（**注：作者在仓库 readme 里已经介绍，控件运行过程中会有异常，使用请自行负责解决哟**）：

运行 WinformDemo 工程：

![运行WinformDemo工程](https://img1.dotnet9.com/2021/11/0703.gif)

先看有哪些控件，下面是控件目录：

![控件目录](https://img1.dotnet9.com/2021/11/0704.gif)

1. 菜单

GDI 不规则圆弧：

右击点击可以展开|关闭

![不规则圆弧](https://img1.dotnet9.com/2021/11/0705.gif)

看到旁边的按钮“独立打开”没，点一下试试：

![不规则圆弧](https://img1.dotnet9.com/2021/11/0706.gif)

可以对它进行移动，具体这个你怎么用发挥你的想象吧。

MAC 鱼眼效果：

比较酷炫的 MAC 鱼眼效果菜单：

![MAC鱼眼效果](https://img1.dotnet9.com/2021/11/0707.gif)

面包屑导航栏：

![面包屑导航栏](https://img1.dotnet9.com/2021/11/0708.gif)

2. 表单

Date 日期选择美化：

![Date日期选择美化](https://img1.dotnet9.com/2021/11/0709.gif)

Color 颜色选择美化：

![Color颜色选择美化](https://img1.dotnet9.com/2021/11/0710.gif)

多点滑块滑杆：

![多点滑块滑杆](https://img1.dotnet9.com/2021/11/0711.gif)

CheckBox 复选框：

![CheckBox复选框](https://img1.dotnet9.com/2021/11/0712.gif)

按钮动画：

![按钮动画](https://img1.dotnet9.com/2021/11/0713.gif)

百分比进度：

![百分比进度](https://img1.dotnet9.com/2021/11/0714.gif)

水波纹进度：

![水波纹进度](https://img1.dotnet9.com/2021/11/0715.gif)

渐变进度：

这个用于实时数据监控还不错。

![渐变进度](https://img1.dotnet9.com/2021/11/0716.gif)

数字时间：

![数字时间](https://img1.dotnet9.com/2021/11/0717.gif)

温度计：

![温度计](https://img1.dotnet9.com/2021/11/0718.gif)

TabControl 美化：

![TabControl美化](https://img1.dotnet9.com/2021/11/0719.gif)

验证码：

![验证码](https://img1.dotnet9.com/2021/11/0721.gif)

雷达扫描：

![雷达扫描](https://img1.dotnet9.com/2021/11/0722.gif)

加载等待：

![加载等待](https://img1.dotnet9.com/2021/11/0723.gif)

3. 播放

图片旋转播放：

![图片旋转播放](https://img1.dotnet9.com/2021/11/0724.gif)

走马灯图片轮播：

![走马灯图片轮播](https://img1.dotnet9.com/2021/11/0725.gif)

文本跑马灯特效：

![文本跑马灯特效](https://img1.dotnet9.com/2021/11/0726.gif)

4. 验证

图案滑屏解锁：

![图案滑屏解锁](https://img1.dotnet9.com/2021/11/0727.gif)

拼图滑块验证：

这个有意思，可以选择多个滑块，增加验证复杂。

![拼图滑块验证](https://img1.dotnet9.com/2021/11/0728.gif)

5. 工具栏

这个比较常见，就不录制 gif 了...

6. 组件

右下角弹窗提示：

![右下角弹窗提示](https://img1.dotnet9.com/2021/11/0729.gif)

其他组件需要您去看看喽。

7. 分析

最后一个大类，已经录制快 30 个 gif 了...

仪表：

![仪表](https://img1.dotnet9.com/2021/11/0730.gif)

雷达分析图：

![雷达分析图](https://img1.dotnet9.com/2021/11/0731.gif)

Chart 分析：

![Chart分析](https://img1.dotnet9.com/2021/11/0732.gif)

介绍完啦，gif 录制酸爽了。

## 关于作者

- Gitee 仓库：[花木兰控件库](https://gitee.com/tlmbem/hml.git)
- 博客：[https://www.cnblogs.com/tlmbem/](https://www.cnblogs.com/tlmbem/)控件的介绍。
- 邮箱：1252578118@qq.com，有问题可以发到这个邮箱，我有空会回复你。
- qq 交流群： **180744253**
