---
title: 快学会这个技能-.NET API拦截技法
slug: Learn-this-skill-Dotnet-API-interception-technique
description: 怎么在不改变源码的情况下，篡改一个方法的入参？返回结果？
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

## 1. 做好准备工作

### 1.1. 创建一个类库

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

### 1.2. 创建一个控制台程序

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

## 2. 拦截API

### 2.1. 引入拦截包-Lib.Harmony

我们使用`Lib.Harmony`包，API的拦截就靠它了，在`HelloHook`工程中添加如下Nuget包：

```xml
<PackageReference Include="Lib.Harmony" Version="2.2.2" />
```

### 2.2. 拦截处理

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

- Prefix约定方法表示拦截前缀，即拦截`Student`类的`GetDetails`方法参数定义，后面会细说；
- Postfix约定方法表示拦截后缀，即拦截`Student`类的`GetDetails`方法结果，后面也会细说；
- Finalizer约定方法表示拦截后最后的处理方法，就是最后执行的方法意思（本文不打算研究）。

三个方法执行顺序是Prefix->Postfix->Finalizer，当然约定的方法不止这三个，其实我们常用的应该是`Prefix`和`Postfix`，详细后面说。

### 2.3. 注册拦截

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

### 2.4. 说好的参数篡改呢？

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

### 2.5. 我要篡改被拦截方法的返回值呢？

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

### 2.6 篡改参数和伪造结果位置别搞错

- 篡改参数只会在`Prefix`方法中生效，所以它叫前缀呢，您可以在`Postfix`方法将传入的参数设置为`ref`进行修改尝试（不传入`__result`参数的前提）；
- 伪造结果只会在`Postfix`方法中生效，只要在此方法中传入了`__result`参数，那么原生的方法（`GetDetails`）就不会执行了，您也可以在`Prefix`方法中传入`__result`并对他进行修改尝试。

## 3. 拦截第三方库的AIP

上面是拦截自己可控的类库方法，但实际情况可能是需要拦截第三方库，比如微软的SDK方法，或第三方控件、类库等，基于以下原因可能产生的场景需要拦截：

1. 第三库未提供源码，但我们想改它的部分方法；
2. 第三库提供了源码，虽然可以修改它的源码，但万一第三库后面迭代升级，我们又不得不更新时，那自己做的修改跟着升级可能麻烦了；

上面第2点，不排除第三库升级API结构也变了，我们也要跟着修改拦截逻辑哦。

## 4. 参考

- [动态IL织入框架Harmony简单入手](https://www.cnblogs.com/qhca/p/12336332.html)
- [一个开放源代码，实现动态IL注入(Hook或补丁工具)框架:Lib.Harmony](https://config.net.cn/opensource/dataformat/a133596e-9d5a-43ef-9012-1e2a19c00e33-p1.html)