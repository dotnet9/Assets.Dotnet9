---
title: 终章：模拟.NET应用场景，综合应用反编译、第三方库调试、拦截、一库多版本兼容方案
slug: Simulate-scenarios-using-third-party-dotNET-libraries-for-debugging-interception-decompilation-and-compatibility-solutions-for-multiple-versions-of-one-library
description: 模拟.NET实际应用场景，综合应用三个主要知识点：一是使用dnSpy反编译第三库及调试，二是使用Lib.Harmony库实现第三库拦截、伪造，三是实现同一个库支持多版本同时引用。
date: 2023-09-23 14:43:19
lastmod: 2023-09-23 12:31:32
copyright: Default
draft: false
cover: https://img1.dotnet9.com/2023/09/cover_08.png
categories: .NET相关
---

>**免责声明**

> 由于传播、利用本公众号所提供的信息而造成的任何直接或者间接的后果及损失，均由使用者本人负责，公众号及作者不为**此**承担任何责任，一旦造成后果请自行承担！谢谢！

大家好，我是沙漠尽头的狼。

本文首发于[Dotnet9](https://dotnet9.com/2023/09/Simulate-scenarios-using-third-party-dotNET-libraries-for-debugging-interception-decompilation-and-compatibility-solutions-for-multiple-versions-of-one-library)，结合前面2篇（[如何在没有第三方.NET库源码的情况，调试第三库代码？](https://dotnet9.com/2023/09/How-to-be-without-a-third-party-dotNET-library-source-code-debugging-the-third-library-code)和[拦截|篡改|伪造.NET类库中不限于public的类和方法](https://dotnet9.com/2023/09/Intercept-tamper-with-and-forge-classes-and-methods-in-the-dotNET-class-library-that-are-not-limited-to-public)），本文设计一个案例手把手带大家同时应用这两篇涉及的技能，并再加入一库多版本支持方案（涉及第三方库反编译和强签名），行文目录：

1. 写在前面的话
2. 案例设计
3. 使用dnSpy调试
4. 使用Lib.Harmony拦截
5. 引入高版本Lib.Harmony：一库多版本兼容使用
6. 总结

## 1. 写在前面的话

技术存在即合理，看怎么使用，前面文章有网友留言：

>（Lib.Harmony）不像什么正经的库，有什么合法的场景里需要这个需求的么？

站长回答：**非常正经**，你使用第三方库，版本确定，并且已经上线，有时不能随便升级第三方库，怕有隐藏风险，只有修改自己的代码，第三方库不动库的情况。

也有网友说的也在理：

>这个用处很强大，有时也很可怕

既然网友有疑惑，所以有了本文，站长花些精力尽量模拟一个看似比较实际的应用场景，您看看正经不正经，可以跟着做一做，应该说是手把手详细教程了。

## 2. 案例设计

这是一个小动画游戏，站长已经将其发布到NuGet：[Dotnet9Games](https://www.nuget.org/packages/Dotnet9Games/)，在这个小动画里埋了两个雷，跟着我的节奏我们一一解决它，首先我们创建一个`.NET Framework 4.6.1`的WPF空程序【Dotnet9Playground】，我想大部分小伙伴做的桌面还是以这个版本为主，不是的话请留言。

### 2.1. 引入Dotnet9Games包

站长已将做好的（假）游戏发布在[NuGet]([NuGet Gallery | Dotnet9Games 1.0.2](https://www.nuget.org/packages/Dotnet9Games/))上，做为第三方包使用，模拟比较真实的场景，直接安装最新的版本即可：

![](https://img1.dotnet9.com/2023/09/0801.png)

### 2.2. 添加目标游戏

打开`MainWindow.xaml`，引入[Dotnet9Games](https://www.nuget.org/packages/Dotnet9Games/)命名空间：

```xml
xmlns:dotnet9="https://dotnet9.com"
```

`MainWindow.xaml`完整代码如下：

```html
<Window
    x:Class="Dotnet9Playground.MainWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
    xmlns:dotnet9="https://dotnet9.com"
    xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
    Title="综合小案例:模拟.NET应用场景，综合应用反编译、第三方库调试、拦截、一库多版本兼容"
    Width="800"
    Height="450"
    Background="Bisque"
    Icon="Resources/favicon.ico"
    mc:Ignorable="d">
    <Border Padding="10">
        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="40" />
                <RowDefinition Height="*" />
            </Grid.RowDefinitions>
            <StackPanel
                Grid.Row="0"
                VerticalAlignment="Center"
                Orientation="Horizontal">
                <TextBlock
                    VerticalAlignment="Center"
                    FontSize="20"
                    Foreground="Blue"
                    Text="生成" />
                <TextBox
                    x:Name="TextBoxBallCount"
                    Width="50"
                    Height="25"
                    Margin="10,0"
                    VerticalAlignment="Center"
                    HorizontalContentAlignment="Center"
                    VerticalContentAlignment="Center"
                    FontSize="20"
                    Foreground="Red"
                    Text="{Binding ElementName=MyBallGame, Path=BallCount, Mode=TwoWay}" />
                <TextBlock
                    Margin="0,0,10,0"
                    VerticalAlignment="Center"
                    FontSize="20"
                    Foreground="Blue"
                    Text="个气球，点击" />
                <Button
                    Padding="15,2"
                    Background="White"
                    BorderBrush="DarkGreen"
                    BorderThickness="2"
                    Click="StartGame_OnClick"
                    Content="开始游戏"
                    FontSize="20"
                    Foreground="DarkOrange" />
            </StackPanel>
            <dotnet9:BallGame
                x:Name="MyBallGame"
                Grid.Row="1"
                BallCount="8" />
        </Grid>
    </Border>
</Window>
```

`MainWindow.xaml.cs`代码如下：

```csharp
using System.Windows;

namespace Dotnet9Playground;

/// <summary>
///     综合小案例:模拟.NET应用场景，综合应用反编译、第三方库调试、拦截、一库多版本兼容
/// </summary>
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
    }

    private void StartGame_OnClick(object sender, RoutedEventArgs e)
    {
        MyBallGame.StartGame();
    }
}
```

准备操作完成，运行程序：

![](https://img1.dotnet9.com/2023/09/0802.gif)

这个游戏比较简单，简单说说：

- 在主界面提供一个文本输入框，填写生成的气球个数，可直接绑定到游戏`BallCount`属性；
- 点击`开始游戏`按钮进行气球生成和动画播放`MyBallGame.StartGame();`。

### 2.3. 引入第一个雷

气球生成8个太少了，来80个：

![](https://img1.dotnet9.com/2023/09/0803.gif)

**怎么弹出一个红色的大圆，就没了？**

## 3. 使用dnSpy调试

### 3.1. 分析

输入`80`个气球后，我们点击`开始游戏`是调用了游戏的方法`StartGame()`, 我们打开[dnSpy]([Releases · dnSpyEx/dnSpy (github.com)](https://github.com/dnSpyEx/dnSpy/releases))（这个链接提供32位和64位下载链接），拖入`Dotnet9Games.dll`,找到该方法代码：

![](https://img1.dotnet9.com/2023/09/0804.png)

```csharp
// Token: 0x06000022 RID: 34 RVA: 0x000022AC File Offset: 0x000004AC
public void StartGame()
{
    bool flag = this.BallCount > 9;
    if (flag)
    {
        this.PlayBrokenHeartAnimation();
    }
    else
    {
        this.GenerateBalloons();
    }
}
```

原来是当气球个数多于9个时调用了`PlayBrokenHeartAnimation()`方法，这个方法干啥的呢？看代码：

![](https://img1.dotnet9.com/2023/09/0805.png)

大致看出来了吗？首先是清空气球控件，然后又添加了一个红色的圆动画，我们调试试试呢？

### 3.2. 调试验证

大致说下步骤：

1. 在`StartGame()`方法第一行打上断点；
2. 点击`dnSpy`【启动】按钮; 
3. 在弹出的【调试程序】界面里，"调试引擎"默认选择`.NET Framework`，"可执行程序"选择我们的WPF主程序Exe【Dotnet9Playground.exe】，再点击【确定】即将WPF程序运行起来了；
4. 主程序界面气球个数输入超过9个，比如80？
5. 点击“开始游戏”按钮；
6. 进入断点了，调试看看，真的进入`PlayBrokenHeartAnimation()`方法

![](https://img1.dotnet9.com/2023/09/0806.gif)

## 4. 使用Lib.Harmony拦截

明白了原因，我们使用`Lib.Harmony`拦截`StartGame()`方法。

### 4.1. 安装Lib.Harmony包

我们安装最低版本`1.2.0.1`：

![](https://img1.dotnet9.com/2023/09/0807.png)

**为啥是安装最低版本？**

为了后面引入一库多版本兼容需求，低版本的`Lib.Harmony`有Bug，我们继续，哈哈。

### 4.2. 编写拦截类

添加拦截类“/Hooks/HookBallGameStartGame.cs”：

```csharp
using Dotnet9Games.Views;
using Harmony;
using System.Reflection;

namespace Dotnet9Playground.Hooks;

internal class HookBallGameStartGame
{
    /// <summary>
    /// 拦截游戏的开始方法StartGame
    /// </summary>
    public static void StartHook()
    {
        var harmony = HarmonyInstance.Create("https://dotnet9.com/HookBallGameStartGame");
        var hookClassType = typeof(BallGame);
        var hookMethod =
            hookClassType!.GetMethod(nameof(BallGame.StartGame), BindingFlags.Public | BindingFlags.Instance);
        var replaceMethod = typeof(HookBallGameStartGame).GetMethod(nameof(HookStartGame));
        var replaceHarmonyMethod = new HarmonyMethod(replaceMethod);
        harmony.Patch(hookMethod, replaceHarmonyMethod);
    }

    /// <summary>
    /// StartGame替换方法
    /// </summary>
    /// <param name="__instance">BallGame实例</param>
    /// <returns></returns>
    public static bool HookStartGame(ref object __instance)
    {
        #region 原方法原代码

        //if (BallCount > 9)
        //{
        //    // 播放爆炸动画效果
        //    PlayExplosionAnimation();
        //}
        //else
        //{
        //    // 生成彩色气球
        //    GenerateBalloons();
        //}

        #endregion

        #region 拦截替换方法逻辑

        // 1、删除气球个数限制逻辑
        // 2、生成气球方法为private修饰，我们通过反射调用
        var instanceType = __instance.GetType();
        var hookGenerateBalloonsMethod =
            instanceType.GetMethod("GenerateBalloons", BindingFlags.Instance | BindingFlags.NonPublic);

        // 生成彩色气球
        hookGenerateBalloonsMethod!.Invoke(__instance, null);

        #endregion

        return false;
    }
}
```

上面的代码加了相关的注释，这里再提一提：

- `StartHook()`方法用于关联被拦截方法`StartGame`与拦截替换方法`HookStartGame`；
- `HookStartGame`是拦截替换方法，方法中注释的代码为原方法逻辑代码；
- 替换代码你可以将气球个数改大一点，或者像站长一样直接不要`if (BallCount > 9)`判断，改为直接调用气球生成方法`GenerateBalloons`。

在`App.xaml.cs`注册上面的拦截类：

```csharp
public partial class App : Application
{
    protected override void OnStartup(StartupEventArgs e)
    {
        base.OnStartup(e);

        // 拦截气球动画播放方法
        HookBallGameStartGame.StartHook();
    }
}
```

现在再运行WPF程序，我们把气球个数改为80个，正常生成了：

![](https://img1.dotnet9.com/2023/09/0808.gif)

### 4.3. 就这样？No，再来一雷

看着气球在动，我们缩放下窗体大小（这里建议Debug下尝试，因为程序会崩溃，导致操作系统会卡那么一小会儿）：

![](https://img1.dotnet9.com/2023/09/0809.gif)

程序异常了，再截图看看：

![](https://img1.dotnet9.com/2023/09/0810.png)

贴上异常代码：

```csharp
/// <summary>
/// 重写MeasureOverride方法，引出Size参数为负数异常
/// </summary>
/// <param name="constraint"></param>
/// <returns></returns>
protected override Size MeasureOverride(Size constraint)
{
    // 计算最后一个元素宽度，不需要关注为什么这样写，只是为了引出Size异常使得

    var lastChild = _balloons.LastOrDefault();
    if (lastChild != null)
    {
        var remainWidth = ActualWidth;
        foreach (var balloon in _balloons)
        {
            remainWidth -= balloon.Shape.Width;
        }

        lastChild.Shape.Measure(new Size(remainWidth, lastChild.Shape.Height));
    }

    return base.MeasureOverride(constraint);
}
```

**分析**

- 在拖动窗体大小时，游戏用户控件`BallGame`的`MeasureOverride`方法会触发，对布局进行重新计算；
- 方法内逻辑：
- 1. 如果存在一个运动的气球，那么计算`BallGame`的实际宽度减去所有子气球的宽度之间的差，得到`remainWidth`; 
  2. 使用`remainWidth`重新计算最后一个气球的大小；
  3. `remainWidth`在做减法操作，那么气球个数足够多，以致于游戏控件宽度小于这些气球宽之和时，就会为负数；
  4. 我们再看看`Size`构造函数代码，如下截图：

![](https://img1.dotnet9.com/2023/09/0811.png)

代码复制过来看：

```csharp
  /// <summary>Implements a structure that is used to describe the <see cref="T:System.Windows.Size" /> of an object. </summary>
  [TypeConverter(typeof (SizeConverter))]
  [ValueSerializer(typeof (SizeValueSerializer))]
  [Serializable]
  public struct Size : IFormattable
  {
    // 这里省略N多代码
    /// <summary>Initializes a new instance of the <see cref="T:System.Windows.Size" /> structure and assigns it an initial <paramref name="width" /> and <paramref name="height" />.</summary>
    /// <param name="width">The initial width of the instance of <see cref="T:System.Windows.Size" />.</param>
    /// <param name="height">The initial height of the instance of <see cref="T:System.Windows.Size" />.</param>
    public Size(double width, double height)
    {
      this._width = width >= 0.0 && height >= 0.0 ? width : throw new ArgumentException(MS.Internal.WindowsBase.SR.Get("Size_WidthAndHeightCannotBeNegative"));
      this._height = height;
    }
    // 这里省略N多代码
  }
```

当宽高为负数时会抛出异常，这就能理解了，我们再使用`Lib.Harmony`拦截`BallGame`的`MeasureOverride`方法，如法炮制。

添加`/Hooks/HookBallgameMeasureOverride.cs`类拦截：

```csharp
using Dotnet9Games.Views;
using Harmony;
using System.Reflection;

namespace Dotnet9Playground.Hooks;

/// <summary>
/// 拦截BallGame的MeasureOverride方法
/// </summary>
internal class HookBallgameMeasureOverride
{
    /// <summary>
    /// 拦截游戏的MeasureOverride方法
    /// </summary>
    public static void StartHook()
    {
        var harmony = HarmonyInstance.Create("https://dotnet9.com/HookBallgameMeasureOverride");
        var hookClassType = typeof(BallGame);
        var hookMethod = hookClassType!.GetMethod("MeasureOverride", BindingFlags.NonPublic | BindingFlags.Instance);
        var replaceMethod = typeof(HookBallgameMeasureOverride).GetMethod(nameof(HookMeasureOverride));
        var replaceHarmonyMethod = new HarmonyMethod(replaceMethod);
        harmony.Patch(hookMethod, replaceHarmonyMethod);
    }

    /// <summary>
    /// MeasureOverride替换方法
    /// </summary>
    /// <param name="__instance">BallGame实例</param>
    /// <returns></returns>
    public static bool HookMeasureOverride(ref object __instance)
    {
        // 暂时不做任何处理，返回false表示
        return false;
    }
}
```

再在`App.xaml.cs`添加拦截注册：

```csharp
using Dotnet9Playground.Hooks;
using System.Windows;

namespace Dotnet9Playground
{
    /// <summary>
    /// App.xaml 的交互逻辑
    /// </summary>
    public partial class App : Application
    {
        protected override void OnStartup(StartupEventArgs e)
        {
            base.OnStartup(e);

            // 拦截气球动画播放方法
            HookBallGameStartGame.StartHook();

            // 这是第二个拦截方法：拦截气球MeasureOverride方法
            HookBallgameMeasureOverride.StartHook();
        }
    }
}
```

再运行程序：

![](https://img1.dotnet9.com/2023/09/0812.png)

拦截方法进入了断点，但无法获取`BallGame`的实例，提示`无法读取内存`，拦截方法返回False（不执行原方法）有下面的异常：

![](https://img1.dotnet9.com/2023/09/0813.png)

这时程序异常退出，我们将拦截方法返回True（继续执行原方法），又有提示：

![](https://img1.dotnet9.com/2023/09/0814.png)

因为继续执行原方法，取最后一个气球方法又报错`var lastChild = _balloons.LastOrDefault();`，好无奈呀，心酸。

经过公司专家指点：

> 因为Size是个结构体指针，0Harmony 1.2.0.1版本把指针当成4位，但“我们的程序”是64位，指针是8位，所有内存错了。

好，那我们使用高版本`Lib.Harmony`？

## 5. 引入高版本Lib.Harmony：一库多版本兼容使用

### 5.1. 新创建工程引入高版本Lib.Harmony

> **理由**

> 有可能程序中使用低版本的`Lib.Harmony`库做了不少拦截操作，贸然全部升级，测试不到位，容易出现程序大崩溃（当前本程序只加了一个`HookBallGameStartGame`)，而同个工程`Dotnet9Playground`直接引入同一个库多版本无法实现（网友如果有建议欢迎留言）。

添加新类库“Dotnet9HookHigh”，`NuGet`安装最新版`Lib.Harmony`库：

![](https://img1.dotnet9.com/2023/09/0815.png)

同时也添加`Dotnet9Games`的`NuGet`包，将前面添加的`HookBallgameMeasureOverride`类剪切复制到该库，`Lib.Harmony`高版本用法与低版本有所区别，在代码中有注释，注意对比，升级后的`HookBallgameMeasureOverride`类定义：

```csharp
using Dotnet9Games.Views;
using HarmonyLib;
using System.Reflection;

namespace Dotnet9HookHigh;

/// <summary>
/// 拦截BallGame的MeasureOverride方法
/// </summary>
public class HookBallgameMeasureOverride
{
    /// <summary>
    /// 拦截游戏的MeasureOverride方法
    /// </summary>
    public static void StartHook()
    {
        //var harmony =  HarmonyInstance.Create("https://dotnet9.com/HookBallgameMeasureOverride");
        // 上面是低版本Harmony实例获取代码，下面是高版本
        var harmony =  new Harmony("https://dotnet9.com/HookBallgameMeasureOverride");
        var hookClassType = typeof(BallGame);
        var hookMethod = hookClassType!.GetMethod("MeasureOverride", BindingFlags.NonPublic | BindingFlags.Instance);
        var replaceMethod = typeof(HookBallgameMeasureOverride).GetMethod(nameof(HookMeasureOverride));
        var replaceHarmonyMethod = new HarmonyMethod(replaceMethod);
        harmony.Patch(hookMethod, replaceHarmonyMethod);
    }

    /// <summary>
    /// MeasureOverride替换方法
    /// </summary>
    /// <param name="__instance">BallGame实例</param>
    /// <returns></returns>
    public static bool HookMeasureOverride(ref object __instance)
    {
        return false;
    }
}
```

区别如下图，`Harmony`实例获取代码有变化，其它不变：

![](https://img1.dotnet9.com/2023/09/0816.png)

主工程`Dotnet9Playground`添加`Dotnet9HookHigh`工程的引用，`App.xaml.cs`中添加引用`HookBallgameMeasureOverride`命名空间：`using Dotnet9HookHigh;`，代码如下：

```csharp
using Dotnet9HookHigh;
using Dotnet9Playground.Hooks;
using System.Windows;

namespace Dotnet9Playground
{
    /// <summary>
    /// App.xaml 的交互逻辑
    /// </summary>
    public partial class App : Application
    {
        protected override void OnStartup(StartupEventArgs e)
        {
            base.OnStartup(e);

            // 拦截气球动画播放方法
            HookBallGameStartGame.StartHook();

            // 这是第二个拦截方法：拦截气球MeasureOverride方法
            HookBallgameMeasureOverride.StartHook();
        }
    }
}
```

这就完了？运行试试：

![](https://img1.dotnet9.com/2023/09/0817.png)

新工程未使用到高版本`Lib.Harmony`。。。

### 5.2. 高低版本的库分目录存放

#### 5.2.1. 分析程序输出目录

![](https://img1.dotnet9.com/2023/09/0818.png)

只有一个`0Harmony.dll`，高低2个版本应该是两个库才对，怎么办？

#### 5.2.2. 新创建目录

低版本不变，为了兼容，我们把高版本改目录存放，比如：`Lib/Lib.Harmony/2.3.0-prerelease.2/0Harmony.dll`,将库按目录结构存放在工程`Dotnet9HookHigh`中：

![](https://img1.dotnet9.com/2023/09/0819.png)

- 并将`0Harmony.dll`的属性【复制到输出目录】设置为【如果较新则复制】
- 删除`Dotnet9HookHigh`对`Lib.Harmony`库的`NuGet`引用，改为本地引用（原来的配方，浏览本地路径的方式）；

![](https://img1.dotnet9.com/2023/09/0820.png)

**这就完了吗？咋还是报那个错？**

### 5.3. 同库多版本配置

#### 5.3.1. App.config配置版本

修改`Dotnet9Palyground`的`App.config`文件，添加`0Harmony.dll`两个版本及读取位置：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
    </startup>
	<runtime>
		<assemblyBinding>
			<dependentAssembly>
				<assemblyIdentity name="0Harmony"
				  publicKeyToken="null"/>
				<codeBase version="1.2.0.1" href="0Harmony.dll" />
			</dependentAssembly>
			<dependentAssembly>
				<assemblyIdentity name="0Harmony"
				  publicKeyToken="null"/>
				<codeBase version="2.3.0-prerelease.2" href="Lib\Lib.Harmony\2.3.0-prerelease.2\0Harmony.dll" />
			</dependentAssembly>
		</assemblyBinding>
	</runtime>
</configuration>
```

再运行，还是报错？啊，我要晕了。。。。

#### 5.3.2. 重点：库的强签名

上面分目录、配置文件版本配置目录也还不够，主工程还是无法区分两个版本的`Lib.Harmony`库，这里涉及.NET 库强签名，就是上面`App.config`配置中的`publicKeyToken`特性，加上这个主程序就认识了，关于强签名网上找到个说明[《**.Net程序集强签名详解**》]([.Net程序集强签名详解_51CTO博客_.net 签名](https://blog.51cto.com/u_15693505/5589203))：

1. 可以将强签名的dll注册到GAC，不同的应用程序可以共享同一dll。

2. 强签名的库，或者应用程序只能引用强签名的dll，不能引用未强签名的dll，但是未强签名的dll可以引用强签名的dll。

3. 强签名无法保护源代码，强签名的dll是可以被反编译的。

4. 强签名的dll可以防止第三方恶意篡改。

这里，对于1.2.0.1版本的0Harmony.dll库我们依然不动，只对`2.3.0-prerelease.2`高版本做强签名处理，签名步骤参考[[VS2008版本引入第三方dll无强签名](https://www.cnblogs.com/liuxuemeicolorful/p/10314035.html)]，我们来一起做一遍，这里会借助`Everything`软件，建议提前下载。

1. 创建一个新的随机密钥对`0Harmony.snk`

使用`Everything`查找一个`sn.exe`程序，随便使用一个，比如：`"C:\Program Files (x86)\Microsoft SDKs\Windows\v10.0A\bin\NETFX 4.6.1 Tools\sn.exe"`,在高版本目录下生成一个密钥对文件`0Harmony.snk`，命令如下：

```shell
"C:\Program Files (x86)\Microsoft SDKs\Windows\v10.0A\bin\NETFX 4.6.1 Tools\sn.exe" -k "F:\github_gitee\TerminalMACS.ManagerForWPF\src\Demo\MultiVersionLibrary\Dotnet9HookHigh\Lib\Lib.Harmony\2.3.0-prerelease.2\0Harmony.snk"
```

![](https://img1.dotnet9.com/2023/09/0821.png)

2. 反编译`0Harmony.dll`

查找`ildasm.exe`，比如`C:\Program Files (x86)\Microsoft SDKs\Windows\v10.0A\bin\NETFX 4.6.1 Tools\ildasm.exe`，执行以下命令生成`0Harmony.dll`的il中间文件：

```shell
"C:\Program Files (x86)\Microsoft SDKs\Windows\v10.0A\bin\NETFX 4.6.1 Tools\ildasm.exe" "F:\github_gitee\TerminalMACS.ManagerForWPF\src\Demo\MultiVersionLibrary\Dotnet9HookHigh\Lib\Lib.Harmony\2.3.0-prerelease.2\0Harmony.dll" /out="F:\github_gitee\TerminalMACS.ManagerForWPF\src\Demo\MultiVersionLibrary\Dotnet9HookHigh\Lib\Lib.Harmony\2.3.0-prerelease.2\0Harmony.il"
```

![](https://img1.dotnet9.com/2023/09/0822.png)

3. 重新编译，附带强命名参数

查找`ilasm.exe`，比如`C:\Windows\Microsoft.NET\Framework64\v2.0.50727\ilasm.exe`,执行以下命令做签名：

```shell
"C:\Windows\Microsoft.NET\Framework64\v2.0.50727\ilasm.exe" "F:\github_gitee\TerminalMACS.ManagerForWPF\src\Demo\MultiVersionLibrary\Dotnet9HookHigh\Lib\Lib.Harmony\2.3.0-prerelease.2\0Harmony.il" /dll /resource="F:\github_gitee\TerminalMACS.ManagerForWPF\src\Demo\MultiVersionLibrary\Dotnet9HookHigh\Lib\Lib.Harmony\2.3.0-prerelease.2\0Harmony.res" /key="F:\github_gitee\TerminalMACS.ManagerForWPF\src\Demo\MultiVersionLibrary\Dotnet9HookHigh\Lib\Lib.Harmony\2.3.0-prerelease.2\0Harmony.snk" /optimize
```

![](https://img1.dotnet9.com/2023/09/0823.png)

4. 验证签名信息

```shell
"C:\Program Files (x86)\Microsoft SDKs\Windows\v10.0A\bin\NETFX 4.6.1 Tools\sn.exe" -v "F:\github_gitee\TerminalMACS.ManagerForWPF\src\Demo\MultiVersionLibrary\Dotnet9HookHigh\Lib\Lib.Harmony\2.3.0-prerelease.2\0Harmony.dll" 
```

![](https://img1.dotnet9.com/2023/09/0824.png)

也可将生成的`dll`拖入`dnSpy`查看：

![](https://img1.dotnet9.com/2023/09/0826.png)

做为对比，查看NuGet下载的`Lib.Harmony`是没做签名的：

![](https://img1.dotnet9.com/2023/09/0825.png)

我们将签名补充进`App.Config`文件：

### 5.4. 小优化

Git一般是配置成不能上传可执行程序及dll文件的，但多版本dll特殊，部分库不能直接从NuGet引用，所以本文中的高版本`Lib.Harmony`库只能使用自己强签名版本，我们将文件扩展名改为“.ref"以允许上传，他人能正常使用，程序如果需要正常编译、生成，则给`Dotnet9HookHigh`工程添加生成前命令行：

```shell
ren "$(ProjectDir)Lib\Lib.Harmony\2.3.0-prerelease.2\0Harmony.ref" "$(ProjectDir)Lib\Lib.Harmony\2.3.0-prerelease.2\0Harmony.dll"
```



## 4. 总结

- 技术交流加群请添加站长微信号：dotnet9com
- 文中示例代码：[MultiVersionLibrary](https://github.com/dotnet9/TerminalMACS.ManagerForWPF/tree/master/src/Demo/MultiVersionLibrary)

