---
title: 【C#进阶】动态注册第三方库事件，轻松搞定！附详细步骤与实例
slug: Csharp-Advanced-Dynamic-registration-of-third-party-library-events-easy-to-handle-Attached-with-detailed-steps-and-demo
description: 在C#开发过程中，我们经常需要处理各种事件，有时候还需要动态地注册第三方库定义的事件。今天，我将为大家分享一个关于如何动态注册第三方库事件的Demo，并根据提供的代码和注释，详细讲解每一步骤。
date: 2024-02-03 22:14:14
lastmod: 2024-02-03 22:57:16
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2024/02/cover_01.png
categories: .NET
tags: 事件注册
---

大家好，我是沙漠尽头的狼！

> 在C#开发过程中，我们经常需要处理各种事件，有时候还需要动态地注册第三方库定义的事件。今天，我将为大家分享一个关于如何动态注册第三方库事件的Demo，并根据提供的代码和注释，详细讲解每一步骤。希望通过这篇文章，大家能够更好地掌握动态注册事件的方法，为开发工作带来更多便利。

在C#中，事件是一种特殊的成员，用于提供类或对象状态变化的通知。有时候，我们需要在使用第三方库时，动态地注册这些库定义的事件，以便在事件发生时执行相应的操作。

下面，我们将通过一个Demo来演示如何实现动态注册第三方库事件。

## 一、准备工作

首先，我们需要一个第三方库的示例代码。在这个示例中，我们有一个名为`ThirdLibrary`的库，其中包含一个名为`TestClass`的类。这个类定义了几个事件和委托，我们将动态地为它们添加处理程序。

```csharp
namespace ThirdLibrary;

public class TestClass
{
    /// <summary>
    /// 无参委托
    /// </summary>
    public Action? NoParamEvent;

    /// <summary>
    /// 带1个string参数
    /// </summary>
    public Action<string>? OneParamEvent;

    /// <summary>
    /// 带1个基本类型和自定义类型的委托
    /// </summary>
    public Action<string, EventParam>? TwoParamEvent;

    /// <summary>
    /// EventHandler事件
    /// </summary>
    public static event EventHandler<EventParam> EventHandlerEvent;

    public void CallEvent()
    {
        NoParamEvent?.Invoke();
        OneParamEvent?.Invoke("单参数委托");
        TwoParamEvent?.Invoke("2个参数委托调用成功", new EventParam() { Message = "帅哥，你成功调用啦！" });
        EventHandlerEvent?.Invoke(this, new EventParam { Message = "EventHandler事件调用成功"});
    }
}

/// <summary>
/// 自定义类型，注册时需要使用dynamic接收
/// </summary>
public class EventParam
{
    public string Message { get; set; }
}
```

## 二、加载第三方库并创建实例

首先，我们使用`Assembly.LoadFrom`方法加载第三方库。然后，通过`Assembly.GetType`方法获取`TestClass`的类型，并使用`Activator.CreateInstance`方法创建其实例。

```csharp
using System.Reflection;

// 加载第三方库  
var assembly = Assembly.LoadFrom("ThirdLibrary.dll");

// 创建TestClass的实例  
var testClassType = assembly.GetType("ThirdLibrary.TestClass");
var testClassInstance = Activator.CreateInstance(testClassType!);
```

## 三、动态注册事件

接下来，我们将通过反射动态地注册事件。首先，通过`Type.GetFields`方法获取`TestClass`类型的所有字段，并找到对应的事件字段。

```csharp
var fields = testClassType!.GetFields();
```

1.  注册无参委托事件

通过字段名称找到`NoParamEvent`字段，并使用`FieldInfo.SetValue`方法将事件处理程序方法`EventHandlerMethod`赋值给该字段。这样，当`NoParamEvent`事件被触发时，`EventHandlerMethod`方法将被调用。

```csharp
// 1、获取NoParamEvent委托  
var noParamEventField = fields.First(field => "NoParamEvent" == field.Name);
noParamEventField.SetValue(testClassInstance, EventHandlerMethod);

// NoParamEvent事件处理程序方法  
void EventHandlerMethod()
{
    Console.WriteLine("NoParamEvent： event raised.");
}
```

2. 注册带一个字符串参数的委托事件

类似地，找到`OneParamEvent`字段，并将其设置为`OneParamEventHandler`方法。这个方法接受一个字符串参数，并打印一条消息。

```csharp
// 2、获取OneParamEvent委托，并设置事件参数处理程序  
var oneParamEventField = fields.First(field => "OneParamEvent" == field.Name);
oneParamEventField.SetValue(testClassInstance, OneParamEventHandler);

// OneParamEvent事件处理程序方法，需要一个字符串参数  
void OneParamEventHandler(string param)
{
    Console.WriteLine($"OneParamEvent： event raised with parameter: {param}");
}
```

3. 注册带两个参数的委托事件

对于`TwoParamEvent`字段，我们将其设置为`TwoParamEventHandler`方法。由于第二个参数是自定义类型`EventParam`，我们无法在编译时知道其确切类型。因此，我们使用`dynamic`关键字作为参数类型，以便在运行时解析类型。

```csharp
// 3、获取TwoParamEvent委托，并设置事件参数处理程序  
var twoParamEventField = fields.First(field => "TwoParamEvent" == field.Name);
twoParamEventField.SetValue(testClassInstance, TwoParamEventHandler);

// TwoParamEvent事件处理程序方法，需要两个参数：string和EventParam类型（通过反射传递，EventParam类型使用动态类型dynamic替换）  
void TwoParamEventHandler(string param1, dynamic param2) // 使用dynamic作为第二个参数的类型，并通过反射传递实际参数值  
{
    Console.WriteLine($"TwoParamEvent： event raised, param1={param1}, param2.Param1={param2.Message}");
}
```

4. 注册EventHandler事件

对于`EventHandlerEvent`事件，我们使用`Type.GetEvents`方法获取事件信息，并通过`EventInfo.EventHandlerType`获取事件处理程序的类型。然后，我们创建一个`EventHandler<dynamic>`类型的委托，并使用`Delegate.CreateDelegate`方法创建一个与事件处理程序类型匹配的委托实例。最后，通过`EventInfo.AddEventHandler`方法将委托实例添加到事件中。

```csharp
var events = testClassType.GetEvents();

// 4、获取EventHandler事件
var eventHandlerEventField = events.First(item => "EventHandlerEvent" == item.Name);
var eventHandlerType = eventHandlerEventField.EventHandlerType;
var eventHandlerMethod = new EventHandler<dynamic>(EventHandlerEventHandler);
var handle = Delegate.CreateDelegate(eventHandlerType, eventHandlerMethod.Method);
eventHandlerEventField.AddEventHandler(null, handle);

// EventHandler事件处理方法
void EventHandlerEventHandler(object sender, dynamic param)
{
    Console.WriteLine($"EventHandler: param.Param1={param.Message}");
}
```

## 四、触发事件并验证注册

为了验证事件是否成功注册，我们调用`TestClass`的`CallEvent`方法，该方法将触发所有已注册的事件。如果一切正常，我们将在控制台上看到相应的输出消息，证明事件处理程序被正确调用。

ThirdLibrary库方法：

```csharp
/// <summary>
/// 该方法用于触发事件，方便测试
/// </summary>
public void CallEvent()
{
    NoParamEvent?.Invoke();
    OneParamEvent?.Invoke("单参数委托");
    TwoParamEvent?.Invoke("2个参数委托调用成功", new EventParam() { Message = "帅哥，你成功调用啦！" });
    EventHandlerEvent?.Invoke(this, new EventParam { Message = "EventHandler事件调用成功" });
}
```

触发上面的事件：

```csharp
// 5、模拟触发事件通知，测试事件是否注册成功
var callEventMethod = testClassType.GetMethods().First(method => "CallEvent" == method.Name);
callEventMethod.Invoke(testClassInstance, null);
```

程序输出如下：

```shell
NoParamEvent： event raised.
OneParamEvent： event raised with parameter: 单参数委托
TwoParamEvent： event raised, param1=2个参数委托调用成功, param2.Param1=帅哥，你成功调用啦！
EventHandler: param.Param1=EventHandler事件调用成功
```

## 五、总结

通过以上步骤，我们成功地动态注册了第三方库定义的事件。这种方法在处理不可预知或无法修改的第三方库时非常有用，因为它允许我们在运行时动态地添加或删除事件处理程序。

希望本文能够帮助大家更好地理解如何动态注册第三方库事件，并在实际开发中灵活应用。如有任何疑问或建议，请随时留言交流！