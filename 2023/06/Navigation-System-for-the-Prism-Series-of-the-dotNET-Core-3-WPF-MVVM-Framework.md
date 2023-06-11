---
title: (6/7).NET Core 3 WPF MVVM框架 Prism系列之导航系统
slug: Navigation-System-for-the-Prism-Series-of-the-dotNET-Core-3-WPF-MVVM-Framework
description: 如何在.NET Core3环境下使用MVVM框架Prism基于区域Region的导航系统
date: 2023-06-11 00:34:23
copyright: Reprint
author: RyzenAdorer
originaltitle: .NET Core 3 WPF MVVM框架 Prism系列之导航系统
originallink: https://www.cnblogs.com/ryzen/p/12703914.html
draft: false
cover: https://img1.dotnet9.com/albums/album_wpf_prism.png
categories: WPF
albums: WPF-Prism
---

> 本文来自转载
>
> 原文作者：RyzenAdorer
>
> 原文标题：.NET Core 3 WPF MVVM框架 Prism系列之导航系统
>
> 原文链接：https://www.cnblogs.com/ryzen/p/12703914.html

本文将介绍如何在.NET Core3环境下使用MVVM框架Prism基于区域Region的导航系统

在讲解Prism导航系统之前，我们先来看看一个例子,我在之前的demo项目创建一个登录界面：

![](https://img1.dotnet9.com/2023/06/0701.gif)

我们看到这里是不是一开始想象到使用WPF带有的导航系统，通过Frame和Page进行页面跳转，然后通过导航日志的GoBack和GoForward实现后退和前进，其实这是通过使用Prism的导航框架实现的,下面我们来看看如何在Prism的MVVM模式下实现该功能

## 1. 区域导航

我们在上一篇介绍了Prism的区域管理，而Prism的导航系统也是基于区域的，首先我们来看看如何在区域导航

### 1.1. 注册区域

LoginWindow.xaml：

```html
<Window x:Class="PrismMetroSample.Shell.Views.Login.LoginWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:PrismMetroSample.Shell.Views.Login"
        xmlns:region="clr-namespace:PrismMetroSample.Infrastructure.Constants;assembly=PrismMetroSample.Infrastructure"
        mc:Ignorable="d"
        xmlns:prism="http://prismlibrary.com/"
        xmlns:i="http://schemas.microsoft.com/expression/2010/interactivity"
         Height="600" Width="400" prism:ViewModelLocator.AutoWireViewModel="True" ResizeMode="NoResize" WindowStartupLocation="CenterScreen"
        Icon="pack://application:,,,/PrismMetroSample.Infrastructure;Component/Assets/Photos/Home, homepage, menu.png" >
    <i:Interaction.Triggers>
        <i:EventTrigger EventName="Loaded">
            <i:InvokeCommandAction Command="{Binding LoginLoadingCommand}"/>
        </i:EventTrigger>
    </i:Interaction.Triggers>
    <Grid>
        <ContentControl prism:RegionManager.RegionName="{x:Static region:RegionNames.LoginContentRegion}" Margin="5"/>
    </Grid>
</Window>
```

### 1.2. 注册导航

App.cs：

```C#
  protected override void RegisterTypes(IContainerRegistry containerRegistry)
  {
        containerRegistry.Register<IMedicineSerivce, MedicineSerivce>();
        containerRegistry.Register<IPatientService, PatientService>();
        containerRegistry.Register<IUserService, UserService>();

        //注册全局命令
        containerRegistry.RegisterSingleton<IApplicationCommands, ApplicationCommands>();
        containerRegistry.RegisterInstance<IFlyoutService>(Container.Resolve<FlyoutService>());

        //注册导航
        containerRegistry.RegisterForNavigation<LoginMainContent>();
        containerRegistry.RegisterForNavigation<CreateAccount>();
  }
```

### 1.3. 区域导航

LoginWindowViewModel.cs：

```C#
public class LoginWindowViewModel:BindableBase
{

    private readonly IRegionManager _regionManager;
    private readonly IUserService _userService;
    private DelegateCommand _loginLoadingCommand;
    public DelegateCommand LoginLoadingCommand =>
            _loginLoadingCommand ?? (_loginLoadingCommand = new DelegateCommand(ExecuteLoginLoadingCommand));

    void ExecuteLoginLoadingCommand()
    {
        //在LoginContentRegion区域导航到LoginMainContent
        _regionManager.RequestNavigate(RegionNames.LoginContentRegion, "LoginMainContent");

         Global.AllUsers = _userService.GetAllUsers();
    }

    public LoginWindowViewModel(IRegionManager regionManager, IUserService userService)
    {
          _regionManager = regionManager;
          _userService = userService;            
    }

}
```

LoginMainContentViewModel.cs：

```C#
public class LoginMainContentViewModel : BindableBase
{
    private readonly IRegionManager _regionManager;

    private DelegateCommand _createAccountCommand;
    public DelegateCommand CreateAccountCommand =>
            _createAccountCommand ?? (_createAccountCommand = new DelegateCommand(ExecuteCreateAccountCommand));

    //导航到CreateAccount
    void ExecuteCreateAccountCommand()
    {
         Navigate("CreateAccount");
    }

    private void Navigate(string navigatePath)
    {
         if (navigatePath != null)
              _regionManager.RequestNavigate(RegionNames.LoginContentRegion, navigatePath);
        
    }


    public LoginMainContentViewModel(IRegionManager regionManager)
    {
         _regionManager = regionManager;
    }

 }
 ```

效果如下：

![](https://img1.dotnet9.com/2023/06/0702.gif)

这里我们可以看到我们调用RegionMannager的RequestNavigate方法，其实这样看不能很好的说明是基于区域的做法，如果将换成下面的写法可能更好理解一点：

```C#
   //在LoginContentRegion区域导航到LoginMainContent
  _regionManager.RequestNavigate(RegionNames.LoginContentRegion, "LoginMainContent");
```

换成

```C#
 //在LoginContentRegion区域导航到LoginMainContent
 IRegion region = _regionManager.Regions[RegionNames.LoginContentRegion];
 region.RequestNavigate("LoginMainContent");
 ```

其实RegionMannager的RequestNavigate源码也是大概实现也是大概如此，就是去调Region的RequestNavigate的方法，而Region的导航是实现了一个INavigateAsync接口：

```C#
public interface INavigateAsync
{
   void RequestNavigate(Uri target, Action<NavigationResult> navigationCallback);
   void RequestNavigate(Uri target, Action<NavigationResult> navigationCallback, NavigationParameters navigationParameters);
    
}   
```

我们可以看到有RequestNavigate方法三个形参：

- target：表示将要导航的页面Uri
- navigationCallback:导航后的回调方法
- navigationParameters:导航传递参数(下面会详解)

那么我们将上述加上回调方法：

```C#
 //在LoginContentRegion区域导航到LoginMainContent
 IRegion region = _regionManager.Regions[RegionNames.LoginContentRegion];
 region.RequestNavigate("LoginMainContent", NavigationCompelted);

 private void NavigationCompelted(NavigationResult result)
 {
     if (result.Result==true)
     {
         MessageBox.Show("导航到LoginMainContent页面成功");
     }
     else
     {
         MessageBox.Show("导航到LoginMainContent页面失败");
     }
 }
 ```

效果如下：

![](https://img1.dotnet9.com/2023/06/0703.gif)

## 2. View和ViewModel参与导航过程

### 2.1. INavigationAware

我们经常在两个页面之间导航需要处理一些逻辑，例如，LoginMainContent页面导航到CreateAccount页面时候，LoginMainContent退出页面的时刻要保存页面数据，导航到CreateAccount页面的时刻处理逻辑(例如获取从LoginMainContent页面的信息)，Prism的导航系统通过一个INavigationAware接口：

```C#
    public interface INavigationAware : Object
    {
        Void OnNavigatedTo(NavigationContext navigationContext);

        Boolean IsNavigationTarget(NavigationContext navigationContext);

        Void OnNavigatedFrom(NavigationContext navigationContext);
    }
```

- OnNavigatedFrom：导航之前触发,一般用于保存该页面的数据
- OnNavigatedTo：导航后目的页面触发，一般用于初始化或者接受上页面的传递参数
- IsNavigationTarget：True则重用该View实例，Flase则每一次导航到该页面都会实例化一次

我们用代码来演示这三个方法：

LoginMainContentViewModel.cs：

```C#
public class LoginMainContentViewModel : BindableBase, INavigationAware
{
     private readonly IRegionManager _regionManager;

     private DelegateCommand _createAccountCommand;
     public DelegateCommand CreateAccountCommand =>
            _createAccountCommand ?? (_createAccountCommand = new DelegateCommand(ExecuteCreateAccountCommand));

     void ExecuteCreateAccountCommand()
     {
         Navigate("CreateAccount");
     }

     private void Navigate(string navigatePath)
     {
         if (navigatePath != null)
            _regionManager.RequestNavigate(RegionNames.LoginContentRegion, navigatePath);
     }

     public LoginMainContentViewModel(IRegionManager regionManager)
     {
          _regionManager = regionManager;
     }

     public bool IsNavigationTarget(NavigationContext navigationContext)
     {            
          return true;
     }

     public void OnNavigatedFrom(NavigationContext navigationContext)
     {
          MessageBox.Show("退出了LoginMainContent");
     }

     public void OnNavigatedTo(NavigationContext navigationContext)
     {
          MessageBox.Show("从CreateAccount导航到LoginMainContent");
     }
 }
 ```

CreateAccountViewModel.cs:

```C#
public class CreateAccountViewModel : BindableBase,INavigationAware
{
     private DelegateCommand _loginMainContentCommand;
     public DelegateCommand LoginMainContentCommand =>
            _loginMainContentCommand ?? (_loginMainContentCommand = new DelegateCommand(ExecuteLoginMainContentCommand));

     void ExecuteLoginMainContentCommand()
     {
         Navigate("LoginMainContent");
     }

     public CreateAccountViewModel(IRegionManager regionManager)
     {
         _regionManager = regionManager;
     }

     private void Navigate(string navigatePath)
     {
        if (navigatePath != null)
            _regionManager.RequestNavigate(RegionNames.LoginContentRegion, navigatePath);
     }

     public bool IsNavigationTarget(NavigationContext navigationContext)
     {
         return true;
     }

     public void OnNavigatedFrom(NavigationContext navigationContext)
     {
         MessageBox.Show("退出了CreateAccount");
     }

     public void OnNavigatedTo(NavigationContext navigationContext)
     {
         MessageBox.Show("从LoginMainContent导航到CreateAccount");
     }

 }
 ```

效果如下：

![](https://img1.dotnet9.com/2023/06/0704.gif)

修改IsNavigationTarget为false：

```C#
public class LoginMainContentViewModel : BindableBase, INavigationAware
{
     public bool IsNavigationTarget(NavigationContext navigationContext)
     {            
          return false;
     }
}

public class CreateAccountViewModel : BindableBase,INavigationAware
{
     public bool IsNavigationTarget(NavigationContext navigationContext)
     {
         return false;
     }
 }
```

效果如下:

![](https://img1.dotnet9.com/2023/06/0705.gif)

我们会发现LoginMainContent和CreateAccount页面的数据不见了，这是因为第二次导航到页面的时候当IsNavigationTarget为false时，View将会重新实例化，导致ViewModel也重新加载,因此所有数据都清空了

### 2.2. IRegionMemberLifetime

同时，Prism还可以通过IRegionMemberLifetime接口的KeepAlive布尔属性控制区域的视图的生命周期,我们在上一篇关于区域管理器说到，当视图添加到区域时候，像ContentControl这种单独显示一个活动视图，可以通过Region的Activate和Deactivate方法激活和失效视图，像ItemsControl这种可以同时显示多个活动视图的，可以通过Region的Add和Remove方法控制增加活动视图和失效视图，而当视图的KeepAlive为false，Region的Activate另外一个视图时，则该视图的实例则会去除出区域，为什么我们不在区域管理器讲解该接口呢？因为当导航的时候，同样的是在触发了Region的Activate和Deactivate，当有IRegionMemberLifetime接口时则会触发Region的Add和Remove方法，这里可以去看下Prism的RegionMemberLifetimeBehavior源码

我们将LoginMainContentViewModel实现IRegionMemberLifetime接口，并且把KeepAlive设置为false，同样的将IsNavigationTarget设置为true

LoginMainContentViewModel.cs:

```C#
public class LoginMainContentViewModel : BindableBase, INavigationAware，IRegionMemberLifetime
{

     public bool KeepAlive => false;
     
     private readonly IRegionManager _regionManager;

     private DelegateCommand _createAccountCommand;
     public DelegateCommand CreateAccountCommand =>
            _createAccountCommand ?? (_createAccountCommand = new DelegateCommand(ExecuteCreateAccountCommand));

     void ExecuteCreateAccountCommand()
     {
         Navigate("CreateAccount");
     }

     private void Navigate(string navigatePath)
     {
         if (navigatePath != null)
            _regionManager.RequestNavigate(RegionNames.LoginContentRegion, navigatePath);
     }

     public LoginMainContentViewModel(IRegionManager regionManager)
     {
          _regionManager = regionManager;
     }

     public bool IsNavigationTarget(NavigationContext navigationContext)
     {            
          return true;
     }

     public void OnNavigatedFrom(NavigationContext navigationContext)
     {
          MessageBox.Show("退出了LoginMainContent");
     }

     public void OnNavigatedTo(NavigationContext navigationContext)
     {
          MessageBox.Show("从CreateAccount导航到LoginMainContent");
     }
 }
 ```

效果如下：

![](https://img1.dotnet9.com/2023/06/0706.gif)

我们会发现跟没实现IRegionMemberLifetime接口和IsNavigationTarget设置为false情况一样，当KeepAlive为false时，通过断点知道，重新导航回LoginMainContent页面时不会触发IsNavigationTarget方法，因此可以知道判断顺序是：KeepAlive -->IsNavigationTarget

### 2.3. IConfirmNavigationRequest

Prism的导航系统还支持再导航前允许是否需要导航的交互需求，这里我们在CreateAccount注册完用户后寻问是否需要导航回LoginMainContent页面，代码如下：

CreateAccountViewModel.cs:

```C#
public class CreateAccountViewModel : BindableBase, INavigationAware，IConfirmNavigationRequest
{
     private DelegateCommand _loginMainContentCommand;
     public DelegateCommand LoginMainContentCommand =>
            _loginMainContentCommand ?? (_loginMainContentCommand = new DelegateCommand(ExecuteLoginMainContentCommand));
    
     private DelegateCommand<object> _verityCommand;
     public DelegateCommand<object> VerityCommand =>
            _verityCommand ?? (_verityCommand = new DelegateCommand<object>(ExecuteVerityCommand));

     void ExecuteLoginMainContentCommand()
     {
         Navigate("LoginMainContent");
     }

     public CreateAccountViewModel(IRegionManager regionManager)
     {
         _regionManager = regionManager;
     }

     private void Navigate(string navigatePath)
     {
        if (navigatePath != null)
            _regionManager.RequestNavigate(RegionNames.LoginContentRegion, navigatePath);
     }

     public bool IsNavigationTarget(NavigationContext navigationContext)
     {
         return true;
     }

     public void OnNavigatedFrom(NavigationContext navigationContext)
     {
         MessageBox.Show("退出了CreateAccount");
     }

     public void OnNavigatedTo(NavigationContext navigationContext)
     {
         MessageBox.Show("从LoginMainContent导航到CreateAccount");
     }
    
     //注册账号
     void ExecuteVerityCommand(object parameter)
     {
         if (!VerityRegister(parameter))
         {
                return;
         }
         MessageBox.Show("注册成功!");
         LoginMainContentCommand.Execute();
     }
    
     //导航前询问
     public void ConfirmNavigationRequest(NavigationContext navigationContext, Action<bool> continuationCallback)
     {
         var result = false;
         if (MessageBox.Show("是否需要导航到LoginMainContent页面?", "Naviagte?",MessageBoxButton.YesNo) ==MessageBoxResult.Yes)
         {
             result = true;
         }
         continuationCallback(result);
      }
 }
```

效果如下：

![](https://img1.dotnet9.com/2023/06/0707.gif)

## 3. 导航期间传递参数

Prism提供NavigationParameters类以帮助指定和检索导航参数，在导航期间，可以通过访问以下方法来传递导航参数：

- INavigationAware接口的IsNavigationTarget，OnNavigatedFrom和OnNavigatedTo方法中IsNavigationTarget，OnNavigatedFrom和OnNavigatedTo中形参NavigationContext对象的NavigationParameters属性
- IConfirmNavigationRequest接口的ConfirmNavigationRequest形参NavigationContext对象的NavigationParameters属性
- 区域导航的INavigateAsync接口的RequestNavigate方法赋值给其形参navigationParameters
- 导航日志IRegionNavigationJournal接口CurrentEntry属性的NavigationParameters类型的Parameters属性(下面会介绍导航日志)

这里我们CreateAccount页面注册完用户后询问是否需要用当前注册用户来作为登录LoginId，来演示传递导航参数，代码如下：

CreateAccountViewModel.cs(修改代码部分):

```C#
private string _registeredLoginId;
public string RegisteredLoginId
{
    get { return _registeredLoginId; }
    set { SetProperty(ref _registeredLoginId, value); }
}

public bool IsUseRequest { get; set; }

void ExecuteVerityCommand(object parameter)
{
    if (!VerityRegister(parameter))
    {
        return;
    }
    this.IsUseRequest = true;
    MessageBox.Show("注册成功!");
    LoginMainContentCommand.Execute();
}   

public void ConfirmNavigationRequest(NavigationContext navigationContext, Action<bool> continuationCallback)
{
    if (!string.IsNullOrEmpty(RegisteredLoginId) && this.IsUseRequest)
    {
         if (MessageBox.Show("是否需要用当前注册的用户登录?", "Naviagte?", MessageBoxButton.YesNo) == MessageBoxResult.Yes)
         {
              navigationContext.Parameters.Add("loginId", RegisteredLoginId);
         }
    }
    continuationCallback(true);
}
```

LoginMainContentViewModel.cs(修改代码部分):

```C#
public void OnNavigatedTo(NavigationContext navigationContext)
{
     MessageBox.Show("从CreateAccount导航到LoginMainContent");
            
     var loginId= navigationContext.Parameters["loginId"] as string;
     if (loginId!=null)
     {
         this.CurrentUser = new User() { LoginId=loginId};
     }
            
 }
 ```

效果如下：

![](https://img1.dotnet9.com/2023/06/0708.gif)

## 4. 导航日志

Prism导航系统同样的和WPF导航系统一样，都支持导航日志，Prism是通过IRegionNavigationJournal接口来提供区域导航日志功能，

```C#
public interface IRegionNavigationJournal
{
    bool CanGoBack { get; }

    bool CanGoForward { get; }

    IRegionNavigationJournalEntry CurrentEntry {get;}

    INavigateAsync NavigationTarget { get; set; }

    void GoBack();

    void GoForward();

    void RecordNavigation(IRegionNavigationJournalEntry entry, bool persistInHistory);

    void Clear();
}
```

我们将在登录界面接入导航日志功能,代码如下:

LoginMainContent.xaml(前进箭头代码部分):

```C#
<TextBlock Width="30" Height="30" HorizontalAlignment="Right" Text="&#xe624;" FontWeight="Bold" FontFamily="pack://application:,,,/PrismMetroSample.Infrastructure;Component/Assets/Fonts/#iconfont" FontSize="30" Margin="10" Visibility="{Binding IsCanExcute,Converter={StaticResource boolToVisibilityConverter}}">
      <i:Interaction.Triggers>
           <i:EventTrigger EventName="MouseLeftButtonDown">
                 <i:InvokeCommandAction Command="{Binding GoForwardCommand}"/>
            </i:EventTrigger>
      </i:Interaction.Triggers>
      <TextBlock.Style>
           <Style TargetType="TextBlock">
                <Style.Triggers>
                    <Trigger Property="IsMouseOver" Value="True">
                          <Setter Property="Background" Value="#F9F9F9"/>
                    </Trigger>
                 </Style.Triggers>
            </Style>
      </TextBlock.Style>
 </TextBlock>
```

BoolToVisibilityConverter.cs:

```
public class BoolToVisibilityConverter : IValueConverter
{
    public object Convert(object value, Type targetType, object parameter, CultureInfo culture)
    {
          if (value==null)
          {
              return DependencyProperty.UnsetValue;
          }
          var isCanExcute = (bool)value;
          if (isCanExcute)
          {
              return Visibility.Visible;
          }
          else
          {
              return Visibility.Hidden;
          }
    }

    public object ConvertBack(object value, Type targetType, object parameter, CultureInfo culture)
    {
            throw new NotImplementedException();
    }
 }
```

LoginMainContentViewModel.cs(修改代码部分):

```C#
IRegionNavigationJournal _journal;

private DelegateCommand<PasswordBox> _loginCommand;
public DelegateCommand<PasswordBox> LoginCommand =>
            _loginCommand ?? (_loginCommand = new DelegateCommand<PasswordBox>(ExecuteLoginCommand, CanExecuteGoForwardCommand));

private DelegateCommand _goForwardCommand;
public DelegateCommand GoForwardCommand =>
            _goForwardCommand ?? (_goForwardCommand = new DelegateCommand(ExecuteGoForwardCommand));

private void ExecuteGoForwardCommand()
{
    _journal.GoForward();
}

private bool CanExecuteGoForwardCommand(PasswordBox passwordBox)
{
    this.IsCanExcute=_journal != null && _journal.CanGoForward;
    return true;
}

public void OnNavigatedTo(NavigationContext navigationContext)
{
     //MessageBox.Show("从CreateAccount导航到LoginMainContent");
     _journal = navigationContext.NavigationService.Journal;

     var loginId= navigationContext.Parameters["loginId"] as string;
     if (loginId!=null)
     {
                this.CurrentUser = new User() { LoginId=loginId};
     }
     LoginCommand.RaiseCanExecuteChanged();
}
```

CreateAccountViewModel.cs(修改代码部分):

```C#
IRegionNavigationJournal _journal;

private DelegateCommand _goBackCommand;
public DelegateCommand GoBackCommand =>
            _goBackCommand ?? (_goBackCommand = new DelegateCommand(ExecuteGoBackCommand));

void ExecuteGoBackCommand()
{
     _journal.GoBack();
}

 public void OnNavigatedTo(NavigationContext navigationContext)
 {
     //MessageBox.Show("从LoginMainContent导航到CreateAccount");
     _journal = navigationContext.NavigationService.Journal;
 }
 ```

效果如下：

![](https://img1.dotnet9.com/2023/06/0709.gif)

### 4.1 选择退出导航日志

如果不打算将页面在导航过程中不加入导航日志，例如LoginMainContent页面，可以通过实现IJournalAware并从PersistInHistory（）返回false

```C#
    public class LoginMainContentViewModel : IJournalAware
    {
        public bool PersistInHistory() => false;
    }   
```

## 5. 小结

prism的导航系统可以跟wpf导航并行使用，这是prism官方文档也支持的，因为prism的导航系统是基于区域的，不依赖于wpf，不过更推荐于单独使用prism的导航系统，因为在MVVM模式下更灵活，支持依赖注入,通过区域管理器能够更好的管理视图View,更能适应复杂应用程序需求，wpf导航系统不支持依赖注入模式，也依赖于Frame元素，而且在导航过程中也是容易强依赖View部分，下一篇将会讲解Prism的对话框服务

## 6. 源码

 最后，附上整个demo的源代码：[PrismDemo源码](https://github.com/ZhengDaoWang/PrismMetroSample)