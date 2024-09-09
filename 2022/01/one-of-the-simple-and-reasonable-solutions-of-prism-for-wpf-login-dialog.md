---
title: Prism For WPF Login对话框又简单又合理的方案之一
slug: one-of-the-simple-and-reasonable-solutions-of-prism-for-wpf-login-dialog
description: 这是一篇极简的小短文。
date: 2022-01-10 20:08:13
copyright: Original
originalTitle: Prism For WPF Login对话框又简单又合理的方案之一
draft: False
cover: https://img1.dotnet9.com/2022/01/cover_01.jpeg
categories: .NET
tags: 
    - C#
    - WPF
    - Prism
---

## 一、前言

这是一篇极简的小短文。首先感谢站长和各位 WPF 大佬对我的指导，我学到了很多，还是关于利用 Prism 做 Login 对话框的事情，看到站长发过一篇[《WPF Prism 框架 Region 失效了？》](https://mp.weixin.qq.com/s/fEWHp6wGioa6SjJx_hXEvQ)，目前我有一个自认为更合适的解决方法，给大家汇报一下：

## 二、主体内容

精髓就一句话，在主 App 这个类里重载`protected override void OnInitialized()`这个方法，然后`login.ShowDialog()`的逻辑写在里面就 ok 了，具体看以下代码：

```cs
namespace Wpf1
{
    /// <summary>
    /// Interaction logic for App.xaml
    /// </summary>
    public partial class App
    {
        protected override Window CreateShell()
        {

             return Container.Resolve<MainWindow>();

        }
        protected override void RegisterTypes (IContainerRegistry containerRegistry)
        {

        }
        protected override void ConfigureModuleCatalog(IModuleCatalog moduleCatalog)
        {

        }
        protected override void OnInitialized()
        {
            var login = Container.Resolve<Login>();
            var loginResult = login.ShowDialog();
            if (loginResult.Value)
               base.OnInitialized();
            else
               Application.Current.Shutdown();
        }
    }
}
```

然后再`Login.xaml.cs`里的“登录”和“退出”按钮的 Click 事件里这么写

```cs
private void Btn1_Click(object sender, RoutedEventArgs e)
{
    //登录
    DialogResult = true;
}
private void Btn2_Click(object sender, RoutedEventArgs e)
{
    //退出
    DialogResult = false;
}
```

这样就可以了，灰常的简单，还是关键的一点是在 APP 里重写 `protected override void OnInitialized()`这个方法，这样就不会在 Login 加载的时候同时也加载`MainWindowViewModel`了。不过这也有一点要注意：此时 Prism 的 Region 好像还没有生效，利用 Prism 的视图注入或者视图发现这两个办法给 Login 添加视图应该不行，还好一般的 Login 也不是特别复杂，在 Login.xaml 正常写写就行。

> 作者：王景浩
>
> 微信 ID：daidai_cn
