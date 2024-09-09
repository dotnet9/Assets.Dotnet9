---
title: WPF：播放GIF很伤神！
slug: wpf-it-hurts-to-play-gif
description: 今天介绍一个用于在 WPF 中显示动态 GIF 图片的库，可在 XAML 或代码中使用：`WpfAnimatedGif`。
date: 2021-07-02 22:12:46
copyright: Original
originalTitle: WPF：播放GIF很伤神！
draft: False
cover: https://img1.dotnet9.com/2021/07/cover_04.gif
categories: 
    - .NET
tags: 
    - WPF
    - GIF
    - 开源WPF
---

# 为 WPF 播放 GIF 伤神不？

![WpfAnimatedGif](https://img1.dotnet9.com/2021/07/0401.gif)

> 仓库地址：[https://github.com/XamlAnimatedGif/WpfAnimatedGif](https://github.com/XamlAnimatedGif/WpfAnimatedGif)

Nuget 包：[WpfAnimatedGif](https://nuget.org/packages/WpfAnimatedGif)。

今天介绍一个用于在 WPF 中显示动态 GIF 图片的库，可在 XAML 或代码中使用：`WpfAnimatedGif`。

简单易用：在 XAML 中，使用`AnimatedSource`附加属性设置需要显示的 gif 图片(替换`Source`属性)：

```html
<Window
  x:Class="WpfAnimatedGif.Demo.MainWindow"
  xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
  xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
  xmlns:gif="http://wpfanimatedgif.codeplex.com"
  Title="MainWindow"
  Height="350"
  Width="525"
>
  <Grid> <image gif:ImageBehavior.AnimatedSource="Images/animated.gif" /></Grid
></Window>
```

![](https://img1.dotnet9.com/2021/07/0402.gif)

您还可以指定重复行为（默认为`0x`，这意味着它将使用来自 GIF 元数据的重复计数）：

```html
<image
  gif:ImageBehavior.RepeatBehavior="3x"
  gif:ImageBehavior.AnimatedSource="Images/animated.gif"
/>
```

![](https://img1.dotnet9.com/2021/07/0403.gif)

当然，您也可以在代码中设置 gif 图片：

```C#
var image = new BitmapImage();
image.BeginInit();
image.UriSource = new Uri(fileName);
image.EndInit();
ImageBehavior.SetAnimatedSource(img, image);
```

![](https://img1.dotnet9.com/2021/07/0404.gif)

有关使用的更多详细信息，请参阅[wiki](https://github.com/XamlAnimatedGif/WpfAnimatedGif/wiki)。

## 特色

- 未增加新的控件，在 WPF 原生的`Image`控件中添加附加属性即实现了 gif 图片动态加载功能
- 考虑实际帧持续时间
- 可以指定重复行为；如果未指定，则使用来自 GIF 元数据的重复计数
- 动画播放完成时可通知，可用于动画完成后做一些特定的事
- 设计模式下的动画预览（必须明确启用）
- 支持手动控制动画（暂停/恢复/跳转）
