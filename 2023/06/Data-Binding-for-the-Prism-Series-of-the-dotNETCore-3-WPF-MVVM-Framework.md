---
title: .NET Core 3 WPF MVVM框架 Prism系列之数据绑定
slug: Data-Binding-for-the-Prism-Series-of-the-dotNETCore-3-WPF-MVVM-Framework
description: 数据绑定
date: 2023-06-10 23:30:18
copyright: Reprint
author: RyzenAdorer
originaltitle: .NET Core 3 WPF MVVM框架 Prism系列之数据绑定
originallink: https://www.cnblogs.com/ryzen/p/11905866.html
draft: false
cover: https://img1.dotnet9.com/albums/album_wpf_prism.png
categories: WPF
albums: WPF-Prism
---

> 本文来自转载
>
> 原文作者：RyzenAdorer
>
> 原文标题：.NET Core 3 WPF MVVM框架 Prism系列之数据绑定
>
> 原文链接：https://www.cnblogs.com/ryzen/p/11905866.html

## 一. 安装Prism
 
1. 使用程序包管理控制台

```shell
Install-Package Prism.Unity -Version 7.2.0.1367
```

也可以去掉‘-Version 7.2.0.1367’获取最新的版本

2. 使用管理解决方案的Nuget包

![](https://img1.dotnet9.com/2023/06/0201.jpg)

在上面或许我们有个疑问？为啥安装prism会跟Prism.Unity有关系，我们知道Unity是个IOC容器, 而Prism本身就支持IOC, 且目前官方支持几种IOC容器：

![](https://img1.dotnet9.com/2023/06/0202.jpg)

- 1. 且unity由于是微软官方的，且支持prism的组件化，由此我推荐使用prism.unity，在官方文档中prism7不支持prism.Mef，Prism 7.1将不支持prism.Autofac
- 2. 安装完prism.unity就已经包含着所有prism的核心库了，架构如下：

![](https://img1.dotnet9.com/2023/06/0203.jpg)

## 二. 实现数据绑定

我们先创建Views文件夹和ViewModels文件夹，将MainWindow放在Views文件夹下，再在ViewModels文件夹下面创建MainWindowViewModel类，如下：

![](https://img1.dotnet9.com/2023/06/0204.jpg)
 
xaml代码如下：

```html
<Window x:Class="PrismSample.Views.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:prism="http://prismlibrary.com/"
        xmlns:local="clr-namespace:PrismSample"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800" prism:ViewModelLocator.AutoWireViewModel="True">
    <StackPanel>
        <TextBox Text="{Binding Text}" Margin="10" Height="100" FontSize="50" Foreground="Black" BorderBrush="Black"/>
        <Button  Height="100" Width="300" Content="Click Me" FontSize="50" Command="{Binding ClickCommnd}"/>
    </StackPanel>
</Window>
``` 

ViewModel代码如下：

```C#
using Prism.Commands;
using Prism.Mvvm;

namespace PrismSample.ViewModels
{
   public class MainWindowViewModel:BindableBase
    {
        private string _text;
        public string Text
        {
            get { return _text; }
            set { SetProperty(ref _text, value); }
        }

        private DelegateCommand _clickCommnd;
        public DelegateCommand ClickCommnd =>
            _clickCommnd ?? (_clickCommnd = new DelegateCommand(ExecuteClickCommnd));

        void ExecuteClickCommnd()
        {
            this.Text = "Click Me！";
        }

        public MainWindowViewModel()
        {
            this.Text = "Hello Prism!";
        }
    }
}
``` 

启动程序：

![](https://img1.dotnet9.com/2023/06/0205.jpg)
 
点击 click Me 按钮:

![](https://img1.dotnet9.com/2023/06/0206.jpg)

可以看到，我们已经成功的用prism实现数据绑定了，且View和ViewModel完美的前后端分离

但是现在我们又引出了另外一个问题,当我们不想按照prism的规定硬要将View和ViewModel放在Views和ViewModels里面，又或许自己的项目取名规则各不相同怎么办，这时候就要用到另外几种方法：

1. 更改命名规则

如果，公司命名规则很变态，导致项目结构变成这样(这种公司辞职了算了)：

![](https://img1.dotnet9.com/2023/06/0207.jpg)

首先我们在App需要引入prism，修改‘Application’为‘prism:PrismApplication’且删除StartupUri

xaml代码如下：
 
```html
<prism:PrismApplication x:Class="PrismSample.App"
             xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
             xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             xmlns:prism="http://prismlibrary.com/"
             xmlns:local="clr-namespace:PrismSample">
    <Application.Resources>
         
    </Application.Resources>
</prism:PrismApplication>
```
 
cs后台代码如下：

```C#
using Prism.Unity;
using Prism.Ioc;
using Prism.Mvvm;
using System.Windows;
using PrismSample.Viewsb;
using System;
using System.Reflection;

namespace PrismSample
{
    /// <summary>
    /// Interaction logic for App.xaml
    /// </summary>
    public partial class App : PrismApplication
    {
        //设置启动起始页
        protected override Window CreateShell()
        {
            return Container.Resolve<MainWindow>();
        }

        protected override void RegisterTypes(IContainerRegistry containerRegistry)
        {

        }

        //配置规则
        protected override void ConfigureViewModelLocator()
        {
            base.ConfigureViewModelLocator();
            ViewModelLocationProvider.SetDefaultViewTypeToViewModelTypeResolver((viewType) =>
            {
                var viewName = viewType.FullName.Replace(".Viewsb.", ".ViewModelsa.OhMyGod.");
                var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;
                var viewModelName = $"{viewName}Test, {viewAssemblyName}";
                return Type.GetType(viewModelName);
            });
        }
    }
}
``` 

上面这两句是关键：

".Viewsb." 表示View所在文件夹namespace,".ViewModelsa.OhMyGod." 表示ViewModel所在namespace

```C#
var viewName = viewType.FullName.Replace(".Viewsb.", ".ViewModelsa.OhMyGod.");
```

Test表示ViewModel后缀

```C#
var viewModelName = $"{viewName}Test, {viewAssemblyName}";
 ```

2. 自定义ViewModel注册

我们新建一个Foo类作为自定义类，代码如下：

```C#
using Prism.Commands;
using Prism.Mvvm;

namespace PrismSample
{
   public class Foo:BindableBase
    {

        private string _text;
        public string Text
        {
            get { return _text; }
            set { SetProperty(ref _text, value); }
        }

        public Foo()
        {
            this.Text = "Foo";
        }

        private DelegateCommand _clickCommnd;
        public DelegateCommand ClickCommnd =>
            _clickCommnd ?? (_clickCommnd = new DelegateCommand(ExecuteClickCommnd));

        void ExecuteClickCommnd()
        {
            this.Text = "Oh My God！";
        }
    }
}
```
 
修改App.cs代码:

```C#
protected override void ConfigureViewModelLocator()
{
    base.ConfigureViewModelLocator();
    ViewModelLocationProvider.Register<MainWindow, Foo>();
    //ViewModelLocationProvider.SetDefaultViewTypeToViewModelTypeResolver((viewType) =>
    //{
    //    var viewName = viewType.FullName.Replace(".Viewsb.", ".ViewModelsa.OhMyGod.");
    //    var viewAssemblyName = viewType.GetTypeInfo().Assembly.FullName;
    //    var viewModelName = $"{viewName}Test, {viewAssemblyName}";
    //    return Type.GetType(viewModelName);
    //});
}
```
  
运行：

![](https://img1.dotnet9.com/2023/06/0208.jpg)
 
点击按钮：

![](https://img1.dotnet9.com/2023/06/0209.jpg)

就算是不注释修改命名规则的代码，我们发现运行结果还是一样，因此我们可以得出结论，

这种直接的，不通过反射注册的自定义注册方式优先级会高点，在官方文档也说明这种方式效率会高点

且官方提供4种方式，其余三种的注册方式如下：

```C#
ViewModelLocationProvider.Register(typeof(MainWindow).ToString(), typeof(MainWindowTest)); 
```

```C#
ViewModelLocationProvider.Register(typeof(MainWindow).ToString(), () => Container.Resolve<Foo>());
```

```C#
ViewModelLocationProvider.Register<MainWindow>(() => Container.Resolve<Foo>());
```