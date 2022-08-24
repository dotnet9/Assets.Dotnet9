---
title: WPF Prism框架Region失效了？
slug: wpf-prism-framework-region-failed
description: 一般客户端项目常规操作流程是：弹出登录窗口=》账号验证成功=》关闭登录窗口=》弹出主窗口=》在主窗口作业。
date: 2021-01-07 10:53:07
copyright: Default
originaltitle: WPF Prism框架Region失效了？
draft: False
cover: https://img1.dotnet9.com/2021/01/cover_02.jpeg
categories: WPF
tags: WPF,Prism,Region
---

站长15年开始使用`Prism 4`，去年（2020年😊）也使用`Prism 8`做开源项目，今天分享处理`Prism Region`的一个问题。

## 问题描述

一般客户端项目常规操作流程是：弹出登录窗口=》账号验证成功=》关闭登录窗口=》弹出主窗口=》在主窗口作业。

常规登录流程

![常规登录流程](https://img1.dotnet9.com/2021/01/0201.gif)

像上面的gif图主窗体，左侧是一棵树，右侧是TabControl，使用Prism模块中注入视图代码：

```C#
public class ModuleOfLogModule : IModule
	{	

		public void RegisterTypes(IContainerRegistry containerRegistry)
		{
			containerRegistry.RegisterForNavigation<MainTabItemView, MainTabItemViewModel>(KEY_OF_CURRENT_MODULE);
		}
	}
```

主工程TabControl为模块视图显示区域：

```html
<TabControl prism:RegionManager.RegionName="{x:Static inf:RegionNames.MainTabRegion}" />
```

点击左侧菜单树时，动态导航模块视图：

```C#
private void RaiseSelectedItemHandler(CustomMenuItem menuItem)
{
  // 此处省略N多代码
  region.RequestNavigate(menuItem.Key);
  // 此处省略N多代码
}
```

实际运行时发现导航没有起作用，原来的操作是登录成功，直接New的主窗体弹出，app.xaml.cs中注册的登录窗体视图：

```C#
protected override Window CreateShell()
{
  return Container.Resolve<LoginView>();
}
```

百度到也有人遇到这个问题：

1. [WPF Prism框架下先登录窗体再打开主窗体](https://bbs.csdn.net/topics/392475855)

讨论区很火，没看到想要的结果。

2. [prism – 区域管理器无法在自定义弹出窗口中找到区域](http://www.voidcn.com/article/p-bsitldrc-bua.html)

这篇给出的答案是手动再注册区域管理器，站长没有采用。

```C#
RegionManager.SetRegionName( theNameOfTheContentControlInsideThePopup, WellKnownRegionNames.DataFeedRegion );
RegionManager.SetRegionManager( theNameOfTheContentControlInsideThePopup, theRegionManagerInstanceFromUnity );
```

3. [Prism MVVM应用 登陆后切换主窗体实现](https://www.iteye.com/resource/cxb2011-11142807)

这个代码是将登录与主窗体做为用户控件，app.xaml.cs中注册shellview，shellview中设置一个区域，两个用户控件通过导航在这个区域切换，效果是没问题，主窗体内的区域能正常使用，但自定义的登录界面和主界面，一般标题栏啥的都不一样，这种做法比较麻烦，不推荐使用。

看问题3类似的描述：[Prism MVVM应用 登陆后切换主窗体实现](https://download.csdn.net/download/cxb2011/11142807)
```C#
应用场景
    使用Prism7开发WPF程序，编码采用MVVM形式。当程序启动时，首先进入一个登陆界面，进行登陆认证，认证成功后转入程序布局主窗口。

设计思路
    WPF程序框架搭建后，程序中存一个Shell.xaml，相当于表演者的唯一舞台。登陆窗体（以下简称 LoginView)和程序布局主窗体（以下简称 MainView）,分别利用IRegionManager进行管理，根据需要在不同时机相继出场表演。所有操作均由各自ViewModel（简称VM）代码完成。
    1.当程序启动后，Shell通过VM，使用RegionManager的Add方法激活LoginView，(注：站长补充描述=登录验证成功，注销LoginView，再通过Add方法激活MainView)
```

## 站长采用的解决方案

baidu基本上没有找到比较合适的方案了，这个问题纠结了我几天（每天晚上搞2、3个小时，站长平时工作已经不做WPF了）。

还好我有科学上网的方法，在YouTube上[Adding a Prism Login Screen](https://www.youtube.com/watch?v=pnG9CNfqZzA)找到一个答案。

![Adding a Prism Login Screen](https://img1.dotnet9.com/2021/01/0202.jpg)

解决方案的代码很简单，在app.xaml.cs中添加如下代码，在初始化shell之前(`InitializeShell`,`shell`指`CreateShell()`注册的主窗体)，先弹出登录窗口，验证成功再初始化shell(`base.InitializeShell(shell)`)：

```C#
protected override void InitializeShell(Window shell)
{
  LoginView loginView = new LoginView();
  if (loginView.ShowDialog() == true)
  {
    var shellVM = shell.DataContext as MainWindowViewModel;
    shellVM.InitData();
    base.InitializeShell(shell);
  }
  else
  {
    Application.Current.Shutdown(-1);
  }
}
```

## 文末探讨

其实该解决方案还是有问题的，在调用`InitializeShell(Window shell)`之前，站长调试发现模块视图已经执行了初始化，按道理说应该是登录成功后模块才执行初始化的，更多思考留给你，有什么建议欢迎`Dotnet9`网站留言。

- 本文Markdown：[点击浏览](https://github.com/dotnet9/Assets.Dotnet9/blob/main/2021/01/2021-01-07_01.md)