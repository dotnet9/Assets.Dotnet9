---
title: 怎么实现WPF多语言动态切换？
slug: how-to-realize-multi-language-dynamic-switching-of-wpf
description: 有网友看了上一篇文章《C# 多语言利器 - ResX Manager》后，提出疑问：这个多语言切换不重启不能刷新，有没有方案？
date: 2021-02-17 09:28:39
copyright: Original
originalTitle: 怎么实现WPF多语言动态切换？
draft: False
cover: https://img1.dotnet9.com/2021/02/cover_02.jpeg
categories: 
    - WPF
tags: 
    - C#
    - WPF
    - 多语言
    - 国际化
    - 资源文件
---

有网友看了上一篇文章《[C# 多语言利器 - ResX Manager](https://mp.weixin.qq.com/s/cGxJE0HnhPJn9lhwGBIMUQ)》后，提出疑问：

> 这个多语言切换不重启不能刷新，有没有方案？

![不重启多语言切换有方案吗？](https://img1.dotnet9.com/2021/02/0201.png)

其实是有的，国内一开源大神提供了一个 WPF 扩展库，其中就有多语言切换实现，我们先看效果：

![动态多语言切换展示](https://img1.dotnet9.com/2021/02/0202.gif)

具体使用请接着往下看：

## 1 开源库实现多语言动态切换

Github 地址：[点击访问](https://github.com/DingpingZhang/WpfExtensions)

![WpfExtensions仓库详情](https://img1.dotnet9.com/2021/02/0203.gif)

### 怎么安装？

直接 Nuget 搜索安装即可：

![Nuget搜索安装](https://img1.dotnet9.com/2021/02/0204.png)

## 2 如何使用？

### 2.1 主工程初始化之前

添加资源文件引用

```C#
I18nManager.Instance.Add(LQClass.AdminForWPF.I18nResources.UiResource.ResourceManager);
```

### 2.2 Prism 模块中初始化

如果使用`Prism`实现模块化，也需要在模块构造函数中引用模块的资源文件

```C#
I18nManager.Instance.Add(LQClass.ModuleOfLog.I18nResources.UiResource.ResourceManager);
```

### 动态语言切换

这里比较灵活了，切换语言时，保存语言标识到配置文件，程序启动时设置配置的语言即可，动态切换语言时也是相同的代码：

```C#
var culture = new System.Globalization.CultureInfo(language);
I18nManager.Instance.CurrentUICulture = culture;
```

## 3 多语言参考项目

- [Accelerider.Windows](https://github.com/Accelerider/Accelerider.Windows)
- [TerminalMACS.ManagerForWPF](https://github.com/dotnet9/TerminalMACS.ManagerForWPF)
- [乐趣课堂（即本号正在做的一个开源项目）](https://github.com/dotnet9/lqclass.com)
