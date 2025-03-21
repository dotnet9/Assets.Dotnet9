---
title: WPF使用ttf图标字体
slug: wpf-use-ttf-icon-font
description: 将矢量图形打包成字体的形式，使用方式也和我们使用字体一样，能够自由设置图标的大小，图标的颜色。相对于传统图片来说，优点还是很明显的：
date: 2021-12-20 00:21:26
copyright: Reprinted
author: 丑萌气质狗
originalTitle: WPF使用ttf图标字体
originalLink: https://www.cnblogs.com/choumengqizhigou/p/15550133.html
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_27.jpeg
categories: 
    - WPF
tags: 
    - WPF
---

## 什么是图标

将矢量图形打包成字体的形式，使用方式也和我们使用字体一样，能够自由设置图标的大小，图标的颜色。相对于传统图片来说，优点还是很明显的：

1. 体积小，加载速度快，性能高
2. 矢量（自由缩放，不会失真）
3. 灵活性（可通过代码更改图标颜色）

正是因为它和字体一样，也存在一些弊端：

1. 难以兼容以前的设计，替换工作量大
2. 很难贴合设计师的设计稿，协调沟通成本略高

## 常用的图标字体

首先推荐的是[阿里巴巴矢量图标库](https://www.iconfont.cn/)，这个里面方案比较多，也有很多成套的图标，可以多尝试尝试，之前只需要登录就行了，现在好像还要验证手机号，有点恶心。

其次就是[Font Awesome 图标](http://www.fontawesome.com.cn/)，这里分为旧版和新版（V5 版本 or Pro 版本），旧版是免费，图标较少。

微软目前提供了两套，一套是 Windows10 的[Segoe MDL2 Assets 图标](https://docs.microsoft.com/zh-cn/windows/apps/design/style/segoe-ui-symbol-font) ，一套是 windows11 的[Segoe Fluent 图标](https://docs.microsoft.com/zh-cn/windows/apps/design/style/segoe-fluent-icons-font)。

Google 有一套开源的 Material Design icons 的图标字体，之前是提供下载到本地的，没找到了[Material Design icons by Google](https://github.com/google/material-design-icons)。

剩下的就不说了，图标字体比较多，自己去搜一搜吧！

## 如何在 WPF 中使用图标字体

本例子中使用的是微软的 [Segoe MDL2 Assets 图标](https://aka.ms/SegoeFonts)，下图是网站中图标字体的部分展示，其中可以看到一个`Unicode码位`，这个是用来标识当前这个字体的，后面也需要用这个来显示我们的字体。

![字体展示](https://img1.dotnet9.com/2021/12/2701.png)

## Windows10 中的应用方式

这套图标字体在`Windows10`中是自带的，所以可以直接设置`FontFamily`属性为`Segoe MDL2 Assets`，来获得图标的支持：

```html
<TextBlock
  FontFamily="Segoe MDL2 Assets"
  FontSize="50"
  Foreground="Red"
  Text="&#xE702;"
/>
```

![](https://img1.dotnet9.com/2021/12/2702.png)

其中`Text`就是由描述中的`Unicode码位`来构成的，不过需要在文本前加上`&#x`，文本后加上分号`;`,完整的就是`”&#xE702;“`。

## 引用字体文件的方式

但是这种方式只支持`Window10`系统，如果在其他系统上，就会无法显示，所以我们[下载 Segoe MDL2 Assets](https://aka.ms/SegoeFonts)图标字体，将下载的压缩包中的`SegMDL2.ttf`拷贝到我们的项目，为了方便管理，放到了项目中新建的`Fonts`文件夹下：

![](https://img1.dotnet9.com/2021/12/2703.png)

然后选中`SegMDL2.ttf`在下方的属性栏中将`生成操作`改为`资源`，这样这个文件就会以资源的形式包含在我们的 WPF 程序中：

![](https://img1.dotnet9.com/2021/12/2704.png)

使用图标字体的方式跟上面是一样的，但是因为是通过外置字体的形式来添加到 WPF 程序中的，所以`FontFamily`设置的属性值就有点讲究了，大概可以表述为`pack://application:,,,/项目名称空间;component/字体路径/字体文件名#字体实际名称`，下面来一个一个介绍（**后面引用自定义资源文件也可以参考此规则，不过#字体名称就不需要了，具体看下方**）：

`项目名称空间`：如果没有私自改过项目名称空间，那么你的项目名称空间就是项目的名称，我这里的项目名称叫`WPF_Blog_Test`,所以项目名称空间也是如此，如果不清楚你的项目名称空间是什么，可以打开你的`XAML`文件，比如项目中的`App.xaml`，可以看到`xaml`文件中的`x:Class`属性，或者后台类的`namespace`：

![](https://img1.dotnet9.com/2021/12/2705.png)

![](https://img1.dotnet9.com/2021/12/2706.png)

`字体路径/文件名`：字体文件这里是放在`Fonts`文件夹下的，所以我的字体路径和文件名为`Fonts/SegMDL2.ttf`,文件名这里有个小插曲，应使用属性中显示的文件名：

![](https://img1.dotnet9.com/2021/12/2707.png)

`字体实际名称`：字体的实际名称需要我们双击打开字体，然后才能看到（**别再 VS 中打开，不然看到的是字节码，在 windows 的文件夹中选中文件双击打开**），这里`SegMDL2.ttf`的实际名称是`Segoe MDL2 Assets`：

![](https://img1.dotnet9.com/2021/12/2708.png)

所以`FontFamily`应该设置为”`pack://application:,,,/WPF_Blog_Test;component/Fonts/SegMDL2.ttf#Segoe MDL2 Assets`“，贴个代码，收工！

```html
<TextBlock
  FontFamily="pack://application:,,,/WPF_Blog_Test;component/Fonts/SegMDL2.ttf#Segoe MDL2 Assets"
  FontSize="50"
  Foreground="Red"
  Text="&#xE702;"
/>
```

​ PS：为了不让每次用都写这么长的`FontFamily`，可以考虑在资源中写好再引用，或者定义一个 TextBlock 图标字体样式（再扯点），`已经了解资源定义的下面可以略过`。

## 自定义资源

新建文件夹`Styles`，在该文件夹下新建资源文件`IconFonts`（**右键 Styles，选择添加资源字典**）。两种方式都写在`IconFonts`这个文件中了，方便演示就少搞点：

![](https://img1.dotnet9.com/2021/12/2709.png)

写上我们的定义的样式（代码在图片下方）：

![](https://img1.dotnet9.com/2021/12/2710.png)

```html
<FontFamily x:Key="SegIconFont">
  pack://application:,,,/WPF_Blog_Test;component/Fonts/SegMDL2.ttf#Segoe MDL2
  Assets
</FontFamily>

<style x:Key="tbSegIconFontKey" TargetType="{x:Type TextBlock}">
  <Setter Property="FontFamily" Value="pack://application:,,,/WPF_Blog_Test;component/Fonts/SegMDL2.ttf#Segoe MDL2 Assets" />
  <Setter Property="Foreground" Value="Red" />
  <Setter Property="FontSize" Value="50" />
</style>
```

此时只是添加了一个名叫`IconFonts`资源字典，还需要引入到项目中，才能在界面中使用，所以需要在`App.xaml`文件中添加一条引用语句，即告知程序需要将新建的这个资源字典包含进来，语句为`<ResourceDictionary Source="pack://application:,,,/WPF_Blog_Test;component/Styles/IconFonts.xaml" />`，名称空间和资源路径规则上面已表述，添加一个`App.xaml`的截图：

![](https://img1.dotnet9.com/2021/12/2711.png)

剩下的就是在程序中使用了，直接上代码

```html
<!--  最原始方式  -->
<TextBlock
  FontFamily="pack://application:,,,/WPF_Blog_Test;component/Fonts/SegMDL2.ttf#Segoe MDL2 Assets"
  FontSize="50"
  Foreground="Red"
  Text="&#xE702;"
/>

<!--  定义FontFamily资源  -->
<TextBlock
  FontFamily="{StaticResource SegIconFont}"
  FontSize="50"
  Foreground="Red"
  Text="&#xE702;"
/>

<!--  定义TextBlock Style样式  -->
<TextBlock
  FontSize="50"
  Foreground="Red"
  Style="{StaticResource tbSegIconFontKey}"
  Text="&#xE702;"
/>
```

没想到截了这么多图，希望逻辑是清晰的，感谢你的观看！

> 本文来自博客园，作者：丑萌气质狗，转载请注明原文链接：https://www.cnblogs.com/choumengqizhigou/p/15550133.html
>
> 转载请注明出处 QQ 群：560611514
