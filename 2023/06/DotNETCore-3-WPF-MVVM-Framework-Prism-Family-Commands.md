---
title: .NET Core 3 WPF MVVM框架 Prism系列之命令
slug: DotNETCore-3-WPF-MVVM-Framework-Prism-Family-Commands
description: 如何在.NET Core3环境下使用MVVM框架Prism的命令的用法
date: 2023-06-10 23:41:26
copyright: Reprint
author: RyzenAdorer
originaltitle: .NET Core 3 WPF MVVM框架 Prism系列之命令
originallink: https://www.cnblogs.com/ryzen/p/12143825.html
draft: false
cover: https://img1.dotnet9.com/albums/album_wpf_prism.png
categories: WPF
albums: WPF-Prism
---

> 本文来自转载
>
> 原文作者：RyzenAdorer
>
> 原文标题：.NET Core 3 WPF MVVM框架 Prism系列之命令
>
> 原文链接：https://www.cnblogs.com/ryzen/p/12143825.html

本文将介绍如何在.NET Core3环境下使用MVVM框架Prism的命令的用法

## 一.创建DelegateCommand命令

我们在上一篇[.NET Core 3 WPF MVVM框架 Prism系列之数据绑定](https://dotnet9.com/2023/06/Data-Binding-for-the-Prism-Series-of-the-dotNETCore-3-WPF-MVVM-Framework)中知道prism实现数据绑定的方式，我们按照标准的写法来实现，我们分别创建Views文件夹和ViewModels文件夹，将MainWindow放在Views文件夹下，再在ViewModels文件夹下面创建MainWindowViewModel类，如下：

![](https://img1.dotnet9.com/2023/06/0301.jpg)


xaml代码如下：

```html
<Window x:Class="CommandSample.Views.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:prism="http://prismlibrary.com/"
        xmlns:i="http://schemas.microsoft.com/expression/2010/interactivity"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:CommandSample"
        mc:Ignorable="d"
        Title="MainWindow" Height="350" Width="450" prism:ViewModelLocator.AutoWireViewModel="True">
    <StackPanel >
        <TextBox Margin="10" Text="{Binding CurrentTime}" FontSize="32"  />
        <Button  x:Name="mybtn"  FontSize="30"  Content="Click Me" Margin="10" Height="60" Command="{Binding GetCurrentTimeCommand}"/>
        <Viewbox Height="80" >
            <CheckBox IsChecked="{Binding IsCanExcute}"  Content="CanExcute" Margin="10"  HorizontalAlignment="Center" VerticalAlignment="Center" />
        </Viewbox>
    </StackPanel>
</Window>
```

MainWindowViewModel类代码如下：

```C#
using Prism.Commands;
using Prism.Mvvm;
using System;
using System.Windows.Controls;

namespace CommandSample.ViewModels
{
   public class MainWindowViewModel: BindableBase
    {
        private bool _isCanExcute;
        public bool IsCanExcute
        {
            get { return _isCanExcute; }
            set 
            { 
                SetProperty(ref _isCanExcute, value);
                GetCurrentTimeCommand.RaiseCanExecuteChanged();
            }
        }

        private string _currentTime;
        public string CurrentTime
        {
            get { return _currentTime; }
            set { SetProperty(ref _currentTime, value); }
        }

        private DelegateCommand _getCurrentTimeCommand;
        public DelegateCommand GetCurrentTimeCommand =>
            _getCurrentTimeCommand ?? (_getCurrentTimeCommand = new DelegateCommand(ExecuteGetCurrentTimeCommand, CanExecuteGetCurrentTimeCommand));

        void ExecuteGetCurrentTimeCommand()
        {
            this.CurrentTime = DateTime.Now.ToString();
        }

        bool CanExecuteGetCurrentTimeCommand()
        {
            return IsCanExcute;
        }
    }
}
```

运行效果如下：

![](https://img1.dotnet9.com/2023/06/0302.gif)


在代码中，我们通过using Prism.Mvvm引入继承BindableBase，因为我们要用到属性改变通知方法SetProperty，这在我们上一篇就知道了，再来我们using Prism.Commands，我们所定义的DelegateCommand类型就在该命名空间下，我们知道，ICommand接口是有三个函数成员的，事件CanExecuteChanged，一个返回值bool的，且带一个参数为object的CanExecute方法，一个无返回值且带一个参数为object的Execute方法，很明显我们实现的GetCurrentTimeCommand命令就是一个不带参数的命令

还有一个值得注意的是，我们通过Checkbox的IsChecked绑定了一个bool属性IsCanExcute，且在CanExecute方法中return IsCanExcute，我们都知道CanExecute控制着Execute方法的是否能够执行，也控制着Button的IsEnable状态,而在IsCanExcute的set方法我们增加了一句：

```C#
GetCurrentTimeCommand.RaiseCanExecuteChanged();
```

其实通过prism源码我们可以知道RaiseCanExecuteChanged方法就是内部调用ICommand接口下的CanExecuteChanged事件去调用CanExecute方法

```C#
public void RaiseCanExecuteChanged()
{
    OnCanExecuteChanged();
}

protected virtual void OnCanExecuteChanged()
{
    EventHandler handler = this.CanExecuteChanged;
    if (handler != null)
    {
        if (_synchronizationContext != null && _synchronizationContext != SynchronizationContext.Current)
        {
            _synchronizationContext.Post(delegate
            {
                handler(this, EventArgs.Empty);
            }, null);
        }
        else
        {
            handler(this, EventArgs.Empty);
        }
    }
}
```

其实上述prism还提供了一个更简洁优雅的写法：

```C#
 private bool _isCanExcute;
 public bool IsCanExcute
 {
    get { return _isCanExcute; }
    set { SetProperty(ref _isCanExcute, value);}
 }

 private DelegateCommand _getCurrentTimeCommand;
 public DelegateCommand GetCurrentTimeCommand =>
    _getCurrentTimeCommand ?? (_getCurrentTimeCommand = new  DelegateCommand(ExecuteGetCurrentTimeCommand).ObservesCanExecute(()=> IsCanExcute));

 void ExecuteGetCurrentTimeCommand()
 {
    this.CurrentTime = DateTime.Now.ToString();
 }
```

其中用了ObservesCanExecute方法，其实在该方法内部中也是会去调用RaiseCanExecuteChanged方法

我们通过上面代码我们可以会引出两个问题：

- 如何创建带参数的DelegateCommand？
- 假如控件不包含依赖属性Command，我们要用到该控件的事件，如何转为命令？

## 二. 创建DelegateCommand带参命令

在创建带参的命令之前，我们可以来看看DelegateCommand的继承链和暴露出来的公共方法，详细的实现可以去看下源码

![](https://img1.dotnet9.com/2023/06/0303.jpg)

 
那么，其实已经很明显了，我们之前创建DelegateCommand不是泛型版本，当创建一个泛型版本的`DelegateCommand<T>`,那么T就是我们要传入的命令参数的类型，那么，我们现在可以把触发命令的Button本身作为命令参数传入

xaml代码如下：

```html
<Button  x:Name="mybtn"  FontSize="30"  Content="Click Me" Margin="10" Height="60" Command="{Binding GetCurrentTimeCommand}"  CommandParameter="{Binding RelativeSource={RelativeSource Mode=Self}}"/>
```

GetCurrentTimeCommand命令代码改为如下：

```C#
private DelegateCommand<object> _getCurrentTimeCommand;
public DelegateCommand<object> GetCurrentTimeCommand =>
    _getCurrentTimeCommand ?? (_getCurrentTimeCommand = new DelegateCommand<object>(ExecuteGetCurrentTimeCommand).ObservesCanExecute(()=> IsCanExcute));

 void ExecuteGetCurrentTimeCommand(object parameter)
 {
    this.CurrentTime =((Button)parameter)?.Name+ DateTime.Now.ToString();
 }
```

我们来看看执行效果：

![](https://img1.dotnet9.com/2023/06/0304.gif)

## 三. 事件转命令
      
在我们大多数拥有Command依赖属性的控件，大多数是由于继承了ICommandSource接口，ICommandSource接口拥有着三个函数成员ICommand接口类型属性Command，object 类型属性CommandParameter,IInputElement 类型属性CommandTarget，而基本继承着ICommandSource接口这两个基础类的就是ButtonBase和MenuItem，因此像Button，Checkbox，RadioButton等继承自ButtonBase拥有着Command依赖属性，而MenuItem也同理。但是我们常用的Textbox那些就没有。

现在我们有这种需求，我们要在这个界面基础上新增第二个Textbox，当Textbox的文本变化时，需要将按钮的Name和第二个Textbox的文本字符串合并更新到第一个Textbox上，我们第一直觉肯定会想到用Textbox的TextChanged事件，那么如何将TextChanged转为命令？

首先我们在xaml界面引入：

```html
xmlns:i="http://schemas.microsoft.com/expression/2010/interactivity"
```

该程序集 System.Windows.Interactivity dll是在 Expression Blend SDK中的,而Prism的包也也将其引入包含在内了，因此我们可以直接引入，然后我们新增第二个Textbox的代码：

```html
<TextBox Margin="10" FontSize="32" Text="{Binding Foo,UpdateSourceTrigger=PropertyChanged}">
    <i:Interaction.Triggers>
        <i:EventTrigger EventName="TextChanged">
            <i:InvokeCommandAction Command="{Binding TextChangedCommand}" CommandParameter="{Binding ElementName=mybtn}"/>
        </i:EventTrigger>
    </i:Interaction.Triggers>
</TextBox>
```

MainWindowViewModel新增代码：

```C#
private string _foo;
public string Foo
{
     get { return _foo; }
     set { SetProperty(ref _foo, value); }
}

private DelegateCommand<object> _textChangedCommand;
public DelegateCommand<object> TextChangedCommand =>
  _textChangedCommand ?? (_textChangedCommand = new DelegateCommand<object>(ExecuteTextChangedCommand));

void ExecuteTextChangedCommand(object parameter)
{
  this.CurrentTime = Foo + ((Button)parameter)?.Name;
}
```

执行效果如下：

![](https://img1.dotnet9.com/2023/06/0305.gif)

上面我们在xaml代码就是添加了对TextBox的TextChanged事件的Blend EventTrigger的侦听，每当触发该事件，InvokeCommandAction就会去调用TextChangedCommand命令

### 3.1. 将EventArgs参数传递给命令#
     
我们知道，TextChanged事件是有个RoutedEventArgs参数TextChangedEventArgs，假如我们要拿到该TextChangedEventArgs或者是RoutedEventArgs参数里面的属性，那么该怎么拿到，我们使用System.Windows.Interactivity的NameSpace下的InvokeCommandAction是不能做到的，这时候我们要用到prism自带的InvokeCommandAction的TriggerParameterPath属性，我们现在有个要求，我们要在第一个TextBox，显示我们第二个TextBox输入的字符串加上触发该事件的控件的名字，那么我们可以用到其父类RoutedEventArgs的Soucre属性，而激发该事件的控件就是第二个TextBox

xaml代码修改如下：

```html
<TextBox x:Name="myTextBox" Margin="10" FontSize="32" Text="{Binding Foo,UpdateSourceTrigger=PropertyChanged}" TextChanged="TextBox_TextChanged">
    <i:Interaction.Triggers>
        <i:EventTrigger EventName="TextChanged">
            <prism:InvokeCommandAction Command="{Binding TextChangedCommand}"  TriggerParameterPath="Source"/>
        </i:EventTrigger>
    </i:Interaction.Triggers>
</TextBox>
```

MainWindowViewModel修改如下：

```C#
void ExecuteTextChangedCommand(object parameter)
{
    this.CurrentTime = Foo + ((TextBox)parameter)?.Name;
}
```

实现效果：

![](https://img1.dotnet9.com/2023/06/0306.gif)

还有一个很有趣的现象，假如上述xaml代码将TriggerParameterPath去掉，我们其实拿到的是TextChangedEventArgs

## 四.实现基于Task的命令

首先我们在界面新增一个新的按钮，用来绑定新的基于Task的命令,我们将要做的就是点击该按钮后，第一个Textbox的在5秒后显示"Hello Prism!",且期间UI界面不阻塞

xaml界面新增按钮代码如下：

```html
<Button  x:Name="mybtn1"  FontSize="30"  Content="Click Me 1" Margin="10" Height="60" Command="{Binding AsyncCommand}" />
```

MainWindowViewModel新增代码：

```C#
private DelegateCommand _asyncCommand;
  public DelegateCommand AsyncCommand =>
     _asyncCommand ?? (_asyncCommand = new DelegateCommand(ExecuteAsyncCommand));

  async void ExecuteAsyncCommand()
  {
     await ExampleMethodAsync();
  }

  async Task ExampleMethodAsync()
  {           
     await Task.Run(()=> 
     {
        Thread.Sleep(5000);
        this.CurrentTime = "Hello Prism!";
     } );
  }
```

也可以更简洁的写法：

```C#
 private DelegateCommand _asyncCommand;
 public DelegateCommand AsyncCommand =>
    _asyncCommand ?? (_asyncCommand = new DelegateCommand( async()=>await ExecuteAsyncCommand()));

 Task ExecuteAsyncCommand()
 {
    return Task.Run(() =>
    {
       Thread.Sleep(5000);
       this.CurrentTime = "Hello Prism!";
    });
  }
```

直接看效果：

![](https://img1.dotnet9.com/2023/06/0307.gif)

## 五. 创建复合命令

prism提供CompositeCommand类支持复合命令，什么是复合命令，我们可能有这种场景，一个主界面的不同子窗体都有其各自的业务，假如我们可以将上面的例子稍微改下，我们分为三个不同子窗体，三个分别来显示当前年份，月日，时分秒，我们希望在主窗体提供一个按钮，点击后能够使其同时显示，这时候就有一种关系存在了，主窗体按钮依赖于三个子窗体的按钮，而子窗体的按钮不依赖于主窗体的按钮

下面是创建和使用一个prism标准复合命令的流程：

- 创建一个全局的复合命令
- 通过IOC容器注册其为单例
- 给复合命令注册子命令
- 绑定复合命令

### 5.1. 创建一个全局的复合命令
   
首先，我们创建一个类库项目，新增ApplicationCommands类作为全局命令类，代码如下：

```C#
public interface IApplicationCommands
{
    CompositeCommand GetCurrentAllTimeCommand { get; }
}

public class ApplicationCommands : IApplicationCommands
{
   private CompositeCommand _getCurrentAllTimeCommand = new CompositeCommand();
   public CompositeCommand GetCurrentAllTimeCommand
   {
        get { return _getCurrentAllTimeCommand; }
   }
}
```

其中我们创建了IApplicationCommands接口，让ApplicationCommands实现了该接口，目的是为了下一步通过IOC容器注册其为全局的单例接口

### 5.2. 通过IOC容器注册其为单例

我们创建一个新的项目作为主窗体，用来显示子窗体和使用复合命令，关键部分代码如下：

App.cs代码：

```C#
using Prism.Unity;
using Prism.Ioc;
using System.Windows;
using CompositeCommandsSample.Views;
using Prism.Modularity;
using CompositeCommandsCore;

namespace CompositeCommandsSample
{

 public partial class App : PrismApplication
 {
     protected override Window CreateShell()
     {
         return Container.Resolve<MainWindow>();
     }

     //通过IOC容器注册IApplicationCommands为单例
     protected override void RegisterTypes(IContainerRegistry containerRegistry)
     {
        containerRegistry.RegisterSingleton<IApplicationCommands, ApplicationCommands>();
     }

     //注册子窗体模块
     protected override void ConfigureModuleCatalog(IModuleCatalog moduleCatalog)
     {
        moduleCatalog.AddModule<CommandSample.CommandSampleMoudle>();
     }
  }
}
```

### 5.3. 给复合命令注册子命令

我们在之前的CommandSample解决方案下面的Views文件夹下新增两个UserControl，分别用来显示月日和时分秒，在其ViewModels文件夹下面新增两个UserControl的ViewModel，并且将之前的MainWindow也改为UserControl，大致结构如下图：

![](https://img1.dotnet9.com/2023/06/0308.jpg)

关键部分代码：

GetHourTabViewModel.cs:

```C#
IApplicationCommands _applicationCommands;

public GetHourTabViewModel(IApplicationCommands applicationCommands)
{
    _applicationCommands = applicationCommands;
    //给复合命令GetCurrentAllTimeCommand注册子命令GetHourCommand
    _applicationCommands.GetCurrentAllTimeCommand.RegisterCommand(GetHourCommand);
}

private DelegateCommand _getHourCommand;
public DelegateCommand GetHourCommand =>
   _getHourCommand ?? (_getHourCommand = new DelegateCommand(ExecuteGetHourCommand).ObservesCanExecute(() => IsCanExcute));

void ExecuteGetHourCommand()
{
   this.CurrentHour = DateTime.Now.ToString("HH:mm:ss");
}
```

GetMonthDayTabViewModel.cs:

```C#
 IApplicationCommands _applicationCommands;

 public GetMonthDayTabViewModel(IApplicationCommands applicationCommands)
 {
     _applicationCommands = applicationCommands;
     //给复合命令GetCurrentAllTimeCommand注册子命令GetMonthCommand
     _applicationCommands.GetCurrentAllTimeCommand.RegisterCommand(GetMonthCommand);
 }

 private DelegateCommand _getMonthCommand;
 public DelegateCommand GetMonthCommand =>
      _getMonthCommand ?? (_getMonthCommand = new DelegateCommand(ExecuteCommandName).ObservesCanExecute(()=>IsCanExcute));

 void ExecuteCommandName()
 {
    this.CurrentMonthDay = DateTime.Now.ToString("MM:dd");
 }
```

MainWindowViewModel.cs:

```C#
IApplicationCommands _applicationCommands;

public MainWindowViewModel(IApplicationCommands applicationCommands)
{
    _applicationCommands = applicationCommands;
    //给复合命令GetCurrentAllTimeCommand注册子命令GetYearCommand
    _applicationCommands.GetCurrentAllTimeCommand.RegisterCommand(GetYearCommand);       
}

private DelegateCommand _getYearCommand;
public DelegateCommand GetYearCommand =>
   _getYearCommand ?? (_getYearCommand = new DelegateCommand(ExecuteGetYearCommand).ObservesCanExecute(()=> IsCanExcute));

void ExecuteGetYearCommand()
{
   this.CurrentTime =DateTime.Now.ToString("yyyy");
}
```

CommandSampleMoudle.cs:

```C#
using CommandSample.ViewModels;
using CommandSample.Views;
using Prism.Ioc;
using Prism.Modularity;
using Prism.Regions;

namespace CommandSample
{
  public class CommandSampleMoudle : IModule
  {
    public void OnInitialized(IContainerProvider containerProvider)
    {
       var regionManager = containerProvider.Resolve<IRegionManager>();
       IRegion region= regionManager.Regions["ContentRegion"];

       var mainWindow = containerProvider.Resolve<MainWindow>();
       (mainWindow.DataContext as MainWindowViewModel).Title = "GetYearTab";
       region.Add(mainWindow);

       var getMonthTab = containerProvider.Resolve<GetMonthDayTab>();
       (getMonthTab.DataContext as GetMonthDayTabViewModel).Title = "GetMonthDayTab";
       region.Add(getMonthTab);

       var getHourTab = containerProvider.Resolve<GetHourTab>();
       (getHourTab.DataContext as GetHourTabViewModel).Title = "GetHourTab";
       region.Add(getHourTab);
    }

    public void RegisterTypes(IContainerRegistry containerRegistry)
    {
            
    }
  }
}
```

### 5.4. 绑定复合命令

主窗体xaml代码：

```html
<Window x:Class="CompositeCommandsSample.Views.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:prism="http://prismlibrary.com/"
        xmlns:local="clr-namespace:CompositeCommandsSample"
        mc:Ignorable="d" prism:ViewModelLocator.AutoWireViewModel="True"
        Title="MainWindow" Height="650" Width="800">
    <Window.Resources>
        <Style TargetType="TabItem">
            <Setter Property="Header" Value="{Binding DataContext.Title}"/>
        </Style>
    </Window.Resources>
    <Grid>
        <Grid.RowDefinitions>
            <RowDefinition Height="auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>
        <Button Content="GetCurrentTime" FontSize="30" Margin="10" Command="{Binding ApplicationCommands.GetCurrentAllTimeCommand}"/>
        <TabControl Grid.Row="1" prism:RegionManager.RegionName="ContentRegion"/>
    </Grid>
</Window>
```

MainWindowViewModel.cs：

```C#
using CompositeCommandsCore;
using Prism.Mvvm;

namespace CompositeCommandsSample.ViewModels
{
  public  class MainWindowViewModel:BindableBase
  {
    private IApplicationCommands _applicationCommands;
    public IApplicationCommands  ApplicationCommands
    {
       get { return _applicationCommands; }
       set { SetProperty(ref _applicationCommands, value); }
    }

    public MainWindowViewModel(IApplicationCommands applicationCommands)
    {
        this.ApplicationCommands = applicationCommands;
    }
  }
}
```

最后看看实际的效果如何：

![](https://img1.dotnet9.com/2023/06/0309.gif)

最后，其中复合命令也验证我们一开始说的关系，复合命令依赖于子命令，但子命令不依赖于复合命令，因此，只有当三个子命令的都为可执行的时候才能执行复合命令，其中用到的prism模块化的知识，我们下一篇会仔细探讨