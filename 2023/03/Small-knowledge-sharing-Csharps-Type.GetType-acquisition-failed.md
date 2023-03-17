---
title: 小知识点分享-C#的Type.GetType获取失败了？
slug: Small-knowledge-sharing-Csharps-Type.GetType-acquisition-failed
description: 通过Type.GetType引申出工程文件app.config的配置使用。
date: 2023-03-15 12:37:33
lastmod: 2023-03-17 21:36
copyright: Default
draft: false
cover: https://img1.dotnet9.com/2023/03/cover_06.png
categories: .NET相关
---

## 问题

插件化应用程序，插件是动态加载的，插件的动态库放各自目录下，比如运行目录的相对路径`./dlls/ChildAssembly.dll`，通过`Assembly.LoadFile`能正确加载程序集：

```C#
var childAssemblyPath = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "dlls", "ChildAssembly.dll");
if (File.Exists(childAssemblyPath))
{
    var childAssembly = Assembly.LoadFile(childAssemblyPath);
}
```

也能通过`childAssembly.GetType("ChildAssembly.Student")`成功获取插件中的类类型，但实际开发时，插件中的类类型拼接的是完整路径，比如`ChildAssembly.Student, ChildAssembly, Version=0.1.0.0, Culture=neutral, PublicKeyToken=null`，类型获取代码：

```C#
var type = Type.GetType("ChildAssembly.Student, ChildAssembly, Version=0.1.0.0, Culture=neutral, PublicKeyToken=null");
```

上面的代码获取type为null?

## 解决方案

不绕弯子，解决方法是给应用程序的`app.config`的节点中`privatePath`配置好插件的目录就好：

```xml
<configuration>
  <runtime>
    <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
      <probing privatePath="dlls"/>
    </assemblyBinding>
  </runtime>
</configuration>
```

## 知识点讲解

在 C# 工程的 app.config 配置文件中，probing 节点是用于配置程序集探测路径的。具体来说，privatePath 元素用于指定相对于应用程序基目录的子目录，以搜索应用程序所需的附加程序集。

当应用程序启动时，CLR 首先会在应用程序目录中查找所需的程序集，如果没有找到，CLR 就会按照 app.config 中配置的 privatePath 指定的路径来搜索程序集。privatePath 路径可以是相对路径或绝对路径。

privatePath 可以包含一个或多个子目录，每个子目录之间使用分号 (;) 分隔。例如：

```
<configuration>
  <runtime>
    <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
      <probing privatePath="bin;lib\plugins"/>
    </assemblyBinding>
  </runtime>
</configuration>
```

上面的配置文件中，probing 节点的 privatePath 元素指定了两个子目录：bin 和 lib\plugins。当应用程序需要加载某个程序集时，CLR 会先在应用程序目录中查找，如果没有找到，则会按照指定的 privatePath 路径来搜索程序集。

privatePath 的主要作用是允许应用程序在运行时加载附加的程序集。这在一些动态扩展的场景下非常有用，例如插件化应用程序、模块化应用程序等。通过在 privatePath 中指定插件或模块所在的目录，应用程序可以动态地加载这些附加的程序集，从而实现更加灵活的应用程序架构。

需要注意的是，privatePath 只对本地文件系统上的程序集有效。如果要加载远程服务器上的程序集，需要使用其他的机制，例如 Assembly.Load 方法、AppDomain.AssemblyResolve 事件等。

## 2023-03-17 

有网友群聊指出一些问题，这里留言大家讨论：

1. 有一点值得注意的是，.NET 程序运行过程中引用的第三方库，只有在代码中真正调用了才会加载；
2. .NET Core项目不读取app.config