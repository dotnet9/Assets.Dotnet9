---
title: .NET 8.0 AOT DebugView
slug: dotnet8_AOT_DebugView
description: Debugview 是一个应用程序，支持你监视本地系统上或可通过 TCP/IP 访问的网络上任何计算机上的调试输出。
date: 2023-08-29 13:44:34
copyright: Reprint
author: JusterZhu
originaltitle: .NET8 AOT DebugView
originallink: https://mp.weixin.qq.com/s/lrQK4fdo_TTbXNNyJTY9Yg
draft: false
cover: https://img1.dotnet9.com/2023/08/debugview.gif
categories: .NET相关
---

## 1. 概要

开发过程中避免不了调试和日志输出使用Trace对象无论在debug模式下和release模式运行的程序都可以进行实时跟踪（vs运行程序时debugview是监控不到的直接双击exe运行监控即可），顺便来测试一下在.NET8中基于AOT发布和普通模式下发布应用使用DebugView工具查看Trace.Write输出调试信息。

## 2. Debugview

Debugview 是一个应用程序，支持你监视本地系统上或可通过 TCP/IP 访问的网络上任何计算机上的调试输出。 它可以同时显示内核模式和 Win32 调试输出，因此无需调试器来捕获应用程序或设备驱动程序生成的调试输出，也无需修改应用程序或驱动程序以使用非标准调试输出 API。

![](https://img1.dotnet9.com/2023/08/0501.png)

使用非常简单，用管理员的身份启动之后把Options里的这几项勾选即可（当我们写的.NET程序运行之后会自动捕捉输出的消息内容）。

![](https://img1.dotnet9.com/2023/08/0502.png)

## 3. 测试代码

```csharp
using System.Diagnostics;

namespace TraceAOT
{
   internal class Program
  {
       static void Main(string[] args)
      {
           //指定Trace输出的日志文件名
           Trace.Listeners.Add(new TextWriterTraceListener("MyTraceListeners"));
           for (int i = 0; i < 10; i++)
          {
               Thread.Sleep(1000);
               //在满足前面的表达式时输出，Trace信息。（同时也向Listeners添加信息。）
               Trace.WriteLineIf(i==5, "Trace message.");
          }
           //Flush完成本次输出
           Trace.Flush();
           Console.WriteLine("OK");
           Console.Read();
      }
  }
}
```

## 4. 测试结果

![](https://img1.dotnet9.com/2023/08/0503.png)

## 5. 结论

DebugView工具在基于.NET 8无论是AOT或普通发布应用程序都是可以正常的使用，Trace对象无论在debug模式下和release模式运行的程序都可以进行实时跟踪极大的简化了我们追踪调试的过程。

