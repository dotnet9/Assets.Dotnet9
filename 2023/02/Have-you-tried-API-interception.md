---
title: 尝试过API拦截吗？
slug: Have-you-tried-API-interception
description: 怎么在不改变源码的情况下，篡改一个方法的入参？返回结果？
date: 2023-02-13 20:21:19
copyright: Default
draft: False
cover: https://img1.dotnet9.com/2022/11/0402.png
categories: .NET相关
tags: hook, harmony
---

大家好，我是沙漠尽头的狼。

本文先抛出以下问题，然后逐一解答：

1. 什么是API拦截？
2. 举例
3. 拦截普通
1、普通方法调用
2、修改处理结果
3、系统API怎么办？
4、引入hook
5、hook自己的方法
6、hook第三方库的方法，甚至.NET方法

## 1. 做好准备工作

1. 创建一个类库

名字就叫`HookDemos.OwnerLibrary`，添加类`Student`，定义如下：

```C#
namespace HookDemos.OwnerLibrary;

public class Student
{

    public string GetDetail(string name)
    {
        return $"大家好，我是Dotnet9网站站长：{name}";
    }
}
```

2. 创建一个控制台程序

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

## 2. 拦截API

我们使用`Lib.Harmony`包，API的拦截就靠它了，在`HelloHook`工程中添加如下Nuget包：

```xml
<PackageReference Include="Lib.Harmony" Version="2.2.2" />
```

添加拦截类

```C#
using HarmonyLib;
using HookDemos.OwnerLibrary;

namespace HelloHook;

[HarmonyPatch(typeof(Student))] // 定义拦截的类类型
[HarmonyPatch(nameof(Student.GetDetail))] // 定义拦截的方法
public class HookClass
{
    /// <summary>
    /// 定义方法执行返回结果前的操作，在这里可以对拦截的方法参数进行篡改
    /// </summary>
    /// <param name="name">[这是可选的]，参数名与需要拦截的方法参数需要一致</param>
    /// <returns></returns>
    public static bool Prefix(ref string name)
    {
        name = "无名"; // 这里对参数进行了修改
        return true; // 返回true：将执行拦截方法，返回false：则原生方法不会执行，且拦截参数的篡改也不会生效
    }

    /// <summary>
    /// 定义方法执行返回结果的操作，在这里可以对方法返回的结果进行直接篡改
    /// </summary>
    /// <param name="__result">[这是可选的]，方法的返回结果，注意命名必须是__result</param>
    public static void Postfix(ref string __result)
    {
        //__result = "No";  // 对结果进行篡改，将只输出：No
    }
}
```

对`Program.cs`进行修改，添加`Harmony`对整个程序集的拦截：

```C#
using HarmonyLib;
using HookDemos.OwnerLibrary;
using System.Reflection;

var student = new Student();
Console.WriteLine(student.GetDetail("沙漠尽头的狼"));     // 这里是还没有拦截之前的，正常输出：大家好，我是Dotnet9网站站长沙漠尽头的狼！

#region 这里使用Harmony进行了整个程序集的拦截，即上面在拦截类上加了`HarmonyPatch`特性的类，在目标API调用时都会主动执行它里面的阅读方法，如：Prefix、Postfix

var harmony = new Harmony("com.dotnet9");
harmony.PatchAll(Assembly.GetExecutingAssembly());

#endregion

Console.WriteLine(student.GetDetail("沙漠尽头的狼"));     // 注意和上面的代码对比，代码完全一样，但因为是在使用Harmony拦截后执行的，所以输出也变了：大家好，我是Dotnet9网站站长：无名

Console.ReadLine();
```

运行程序输出如下：

```shell
大家好，我是Dotnet9网站站长：沙漠尽头的狼
大家好，我是Dotnet9网站站长：无名
```

注意看拦截类`HookClass`的`Postfix`方法注释，我们可以在这个方法里对返回结果直接篡改，取消方法内注释：

```C#
/// <summary>
/// 定义方法执行返回结果的操作，在这里可以对方法返回的结果进行直接篡改
/// </summary>
/// <param name="__result">[这是可选的]，方法的返回结果，注意命名必须是__result</param>
public static void Postfix(ref string __result)
{
    __result = "No";  // 对结果进行篡改，将只输出：No
}
```

运行程序输出如下：

```shell
大家好，我是Dotnet9网站站长：沙漠尽头的狼
No
```

参考：

- [动态IL织入框架Harmony简单入手](https://www.cnblogs.com/qhca/p/12336332.html)