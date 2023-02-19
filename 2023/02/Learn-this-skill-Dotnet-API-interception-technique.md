---
title: 快学会这个技能-.NET API拦截技法
slug: Learn-this-skill-Dotnet-API-interception-technique
description: 怎么在不改变源码的情况下，篡改一个方法的入参？伪造返回结果？
date: 2023-02-13 20:21:19
copyright: Default
draft: False
cover: https://img1.dotnet9.com/2023/02/0501.png
categories: .NET相关
tags: hook, harmony
---

大家好，我是沙漠尽头的狼。

本文先抛出以下问题，请在文中寻找答案，可在评论区回答：

1. 什么是API拦截？
2. 一个方法被很多地方调用，怎么在不修改这个方法源码情况下，记录这个方法调用的前后时间？
3. 同2，不修改源码的情况下，怎么对方法的参数进行校正（篡改）？
3. 同3，不修改源码的情况下，怎么对方法的返回值进行伪造？

## 1. 前言

您是否曾经遇到过不属于您但想要更改其行为的类库方法？通常，该方法是非公开的，并且没有很好的方法来覆盖其行为。你可以看到它是如何工作的（因为你很棒，并且使用像Resharper、dnSpy之类反编译工具，对吧？），你只是无法改变它。你真的需要改变它，因为XXX原因。

**有几个选项可供您使用：**

1. 通过反编译或下载源代码（如果首先可用）获取源代码。这通常很冒险，因为它经常伴随着复杂的构建过程，许多依赖项，现在你负责维护库的整个分支，即使你只想做一个很小的改变。

2. 使用 `ILDasm` 反编译应用，直接修补 `IL` 代码，然后使用 `ILAs` 将其组装回来。在许多方面，这更好，因为您可以创建一个战略性的手术切口，而不是全面的“从头开始”的方法。缺点是您必须完全在IL中实现您的方法，这是一个不平凡的冒险。

如果您正在处理已签名的库，上述两种方法也不起作用。

现在让我们看一下另一种解决方法-内存修补。这与游戏作弊引擎几十年来使用的技术相同，这些引擎附加到正在运行的进程，查找内存位置并改变其行为。听起来很复杂？ 实际上，在 .NET 中做到这一点比听起来容易得多。我们将使用一个名为`Harmony`的库，该库可通过“Lib.Harmony”包在NuGet上获得。这是一个用于 `.NET` 的内存修补引擎，主要针对使用 Unity 构建的游戏，当然不止`Unity`。

今天，我将向您展示如何更改您认为不可能的事情 - 从Hook自己的库开始，到Hook第三方库、.NET默认库结束。

## 2. Hook自己的库

### 2.1. 做好准备工作

### 2.1.1. 创建一个类库

名字就叫`HookDemos.OwnerLibrary`，添加类`Student`，定义如下：

```C#
namespace HookDemos.OwnerLibrary;

public class Student
{
    public string GetDetails(string name)
    {
        return $"大家好，我是Dotnet9网站站长：{name}";
    }

    public override string ToString()
    {
        return nameof(Student);
    }
}
```

- 定义了一个学生类`Student`;
- `Student`类中定义了一个`GetDetails`方法，返回格式化的个人介绍信息，这个方法后面拦截试验使用；
- `ToString`只简单的返回类名，方便拦截时打印拦截的类名；

### 2.1.2. 创建一个控制台程序

名字叫`HelloHook`，添加项目`HookDemos.OwnerLibrary`引用，在`Program.cs`中调用`Student`的`GetDetail`方法：

```C#
using HookDemos.OwnerLibrary;

var student = new Student();
Console.WriteLine(student.GetDetail("沙漠尽头的狼"));

Console.ReadLine();
```

运行程序输出如下：

```shell
大家好，我是Dotnet9网站站长：沙漠尽头的狼
```

基本工作准备完成，这就是一个简单的控制台程序，后文的内容就根据这两个工程展开细说。

### 2.2. 拦截API

#### 2.2.1. 引入拦截包-Lib.Harmony

我们使用`Lib.Harmony`包，API的拦截就靠它了，在`HelloHook`工程中添加如下Nuget包：

```xml
<PackageReference Include="Lib.Harmony" Version="2.2.2" />
```

#### 2.2.2. 拦截处理

添加拦截类`HookClass`：

```C#
using HarmonyLib;
using HookDemos.OwnerLibrary;

namespace HelloHook;

[HarmonyPatch(typeof(Student))] // 定义拦截的类类型：Student
[HarmonyPatch(nameof(Student.GetDetails))] // 定义拦截的方法：GetDetail
public class HookClass
{
    public static bool Prefix()
    {
        Console.WriteLine($"Prefix");
        return true;
    }

    public static void Postfix()
    {
        Console.WriteLine($"Postfix");
    }

    public static void Finalizer()
    {
        Console.WriteLine($"Finalizer");
    }
}
```

看代码中的注释，`HookClass`类上添加了两个`HarmonyPatch`特性：

- 第一个是关联拦截的类`Student`；
- 第二个是关联拦截的类方法`GetDetails`

即当程序中调用`Student`类的`GetDetails`方法时，`HookClass`内定义的方法就会分别执行：

- 约定方法的定义必须为`静态`方法，`Prefix`返回值为`bool`，另两个为`void`；
- Prefix约定方法表示拦截前缀，即拦截`Student`类的`GetDetails`方法参数定义，后面会细说；
- Postfix约定方法表示拦截后缀，即拦截`Student`类的`GetDetails`方法结果，后面也会细说；
- Finalizer约定方法表示拦截后最后的处理方法，就是最后执行的方法意思（本文不打算研究）。

三个方法执行顺序是Prefix->Postfix->Finalizer，当然约定的方法不止这三个，其实我们常用的应该是`Prefix`和`Postfix`，详细后面说。

#### 2.2.3. 注册拦截

对`Program.cs`进行修改，添加`Harmony`对整个程序集的拦截：

```C#
using HarmonyLib;
using HookDemos.OwnerLibrary;
using System.Reflection;

Console.WriteLine("程序开始---------------------");

var student = new Student();
Console.WriteLine(student.GetDetails("沙漠尽头的狼"));     // 这里是还没有拦截之前的，正常输出：大家好，我是Dotnet9网站站长沙漠尽头的狼！

Console.WriteLine("注册拦截---------------------");

// 这里使用Harmony进行了整个程序集的拦截，即上面在拦截类上加了`HarmonyPatch`特性的类，在目标API调用时都会主动执行它里面的定义的约定方法，如：Prefix、Postfix
var harmony = new Harmony("com.dotnet9"); // 参数是一个标识ID，表示一个Harmony实例，你可以注册多个尝试
harmony.PatchAll(Assembly.GetExecutingAssembly());  // 自动注册所有的拦截类，即上面定义的HookClass类会被Harmony主动发现，并开始监视HookClass类定义拦截的类方法调用

Console.WriteLine("注册完成---------------------");

Console.WriteLine(student.GetDetails("沙漠尽头的狼"));     // 注意和上面的代码对比，代码完全一样，但因为是在使用Harmony拦截后执行的，所以输出可能变了

Console.WriteLine("程序结束---------------------");
Console.ReadLine();
```

运行程序输出如下：

```shell
程序开始---------------------
大家好，我是Dotnet9网站站长：沙漠尽头的狼
注册拦截---------------------
注册完成---------------------
Prefix
Postfix
Finalizer
大家好，我是Dotnet9网站站长：沙漠尽头的狼
程序结束---------------------
```

上面代码就完成了一个自定义类的拦截处理，我们可以在约定方法（`Prefix`和`Postfix`等）里做一些日志记录，类似于B/S中的AOP拦截，操作日志在这里记录正合适。

**这就完了？说啥呢，这才开始。**

#### 2.2.4. 说好的参数篡改呢？

![](https://img1.dotnet9.com/2023/02/0501.png)

看上图，两处`Console.WriteLine(student.GetDetails("沙漠尽头的狼"));`代码输出的结果不一致了，`GetDetails`方法我们没改，主要是改`HookClass`的`Prefix`方法：

```C#
public static bool Prefix(ref string name)
{
    Console.WriteLine($"Prefix");
    name = "沙漠之狐";
    return true;
}
```

`Prefix`方法里可以定义和拦截的方法`GetDetails`一样的参数定义，注意前面的`ref`，这样就能达到篡改参数的目的了。

我们再把方法改成下图中这样，可能您就明白这个拦截方法的意义了：

![](https://img1.dotnet9.com/2023/02/0502.png)

上面的方法，在不改变原`GetDetails`方法的前提下，对参数进行校验，可记录日志或对参数进行格式修正。

**注意：**

参数是可选的，如果不进行篡改，去掉`ref`也是可以的。

**`Prefix`方法返回值什么用处呢？**

修改`Prefix`方法返回false：

![](https://img1.dotnet9.com/2023/02/0503.png)

由结果可推导出：

- 返回true：将执行原生方法；
- 返回false：原生方法不会再执行，直接返回的结果是返回类型的默认值。既无论传入的参数是什么，或者怎么篡改，如返回值为string，则返回空字符串，返回值类型为int，则返回0.

可通过给方法`GetDetails`打断点，第二次调用时是不会进这个断点的，控制台也打印出了返回值类型的默认值，即空字符串。

#### 2.2.5. 我要篡改被拦截方法的返回值呢？

**注：** 记得恢复`Prefix`方法返回`true`。

修改`Postfix`方法如下：

```C#
public static void Postfix(ref string __result)
{
    __result = "我直接篡改了结果，不论参数是什么，Prefix里对参数怎么篡改";
    Console.WriteLine($"Postfix");
}
```

详细修改及运行结果：

![](https://img1.dotnet9.com/2023/02/0504.png)

**说明：**

- __result前缀为两个下划线，这是固定名称，表示拦截方法的返回值；
- 如果要对结果进行伪造，结果类型前需要加`ref`；

**伪造结果要结合参数呢？**

一样的，将参数传入即可：

![](https://img1.dotnet9.com/2023/02/0505.png)

#### 2.2.6 篡改参数和伪造结果位置别搞错

- 篡改参数只会在`Prefix`方法中生效，所以它叫前缀呢，您可以在`Postfix`方法将传入的参数设置为`ref`进行修改尝试（不传入`__result`参数的前提）；
- 伪造结果只会在`Postfix`方法中生效，只要在此方法中传入了`__result`参数，那么原生的方法（`GetDetails`）就不会执行了，您也可以在`Prefix`方法中传入`__result`并对他进行修改尝试。

上面的示例[源码点这](https://github.com/dotnet9/TerminalMACS.ManagerForWPF/tree/master/src/Demo/HookDemos/HelloHook)。

## 3. 拦截第三方库的API

上面是拦截自己可控的类库方法，但实际情况可能是需要拦截第三方库，比如微软的SDK方法，或第三方控件、类库等，使用方法和上面其实是一样的。

基于以下原因可能产生的场景需要拦截：

1. 第三库未提供源码，但我们想改它的部分方法；
2. 第三库提供了源码，虽然可以修改它的源码，但万一第三库后面迭代升级，我们又不得不更新时，那自己做的修改跟着升级可能麻烦了；

拦截注意，如您所见，这提供了大量新的可能性。请记住，权力越大，责任越大。由于您以原始开发人员不打算的方式覆盖行为，因此无法保证您的补丁代码在他们发布新版本的代码时会起作用。即上面第2点，不排除第三库升级API结构也变了，我们也要跟着修改拦截逻辑哦

## 4. 拦截.NET默认的API

即.NET框架提供的一些类库方法拦截，我们举例说明。

## 5. 总结及分享

### 5.1. 总结

Harmony的原理是利用反射获取对应类中的方法，然后加上特性标签进行逻辑控制，达到不破坏原有代码进行更新的效果。

用于在运行时修补替换和装饰 .NET/.NET Core 方法的库。 但是该技术可以与任何.NET版本一起使用。它对同一方法的多次更改是累积而不是覆盖。 

从《[Harmony wiki patching](https://github.com/pardeike/Harmony/wiki/Patching)》中了解以下使用注意事项：

1. 最新2.0版支持 .NET Core ；
2. Harmony支持手动（Patch，参考[Harmony wiki](https://github.com/pardeike/Harmony/wiki)上的使用）和自动（PatchAll，本文演示使用的这种方式，Lib.Harmony使用的是C#的特性机制）；
3. 它为每个原始方法创建DynamicMethod方法，并向其织入代码，该代码在开始（Prefix）和结束时（Postfix）调用自定义方法。它还允许您编写过滤器（Transpiler）来处理原始的IL代码，从而可以对原始方法进行更详细的操作; 
4. Getter/Setter、虚/ 非虚 方法、 静态 方法；
5. 补丁方法必须是静态方法；
6. Prefix需要返回void或者bool类型(void即不拦截)；
7. Postfix需要返回void类型，或者返回的类型要与第一个参数一致(直通模式)；
8. 如果原方法不是静态方法，则可以使用名为__instance(两个下划线)的参数来访问对象实例；
9. 可以使用名为__result(两个下划线)的参数来访问方法的返回值，如果是Prefix，则得到返回值的默认值；
10. 可以使用名为__state(两个下划线)的参数在Prefix补丁中存储任意类型的值，然后在Postfix中使用它，你有责任在Prefix中初始化它的值；
11. 可以使用与原方法中同名的参数来访问对应的参数，如果你要写入非引用类型，记得使用ref关键字；
12. 补丁使用的参数必须严格对应类型(或者使用object类型)和名字；
13. 我们的补丁只需要定义我们需要用到的参数，不用把所有参数都写上；
14. 要允许补丁重用，可以使用名为__originalMethod(两个下划线)的参数注入原始方法。

### 5.2 分享

读者朋友们，相信不少人使用过`Harmony`或者其他的 .NET Hook库，可在评论中留言分享，可提出自己的疑问，或自己的使用心得：

1. 我使用过这个库进行API的Hook，它是XXX；
2. 我自己实现过类似功能，分享文章链接是XXX；
2. 想问，我能拦截这个API吗？场景是XXXX

[我的分享] + [你的分享] ∈ [.NET圈子的一份小力量]

## 6. 参考

- [Harmony](https://github.com/pardeike/Harmony)
- [Harmony wiki](https://github.com/pardeike/Harmony/wiki)
- [Harmony API文档](https://harmony.pardeike.net/api/HarmonyLib.html)
- [Hacking .NET – rewriting code you don’t control](https://stakhov.pro/hacking-net-rewriting-code-you-dont-control/)
- [Rimworld Mod制作教程6 使用Harmony对C#代码Patch](https://blog.csdn.net/qq_29799917/article/details/105017151)
- [动态IL织入框架Harmony简单入手](https://www.cnblogs.com/qhca/p/12336332.html)
- [一个开放源代码，实现动态IL注入(Hook或补丁工具)框架:Lib.Harmony](https://config.net.cn/opensource/dataformat/a133596e-9d5a-43ef-9012-1e2a19c00e33-p1.html)