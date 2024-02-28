---
title: （1）入门MasaFramework教程
slug: Getting-Started-with-MasaFramework-Tutorial-1
description: 首先了解一下MasaFramework是什么
date: 2023-03-16 22:50:32
copyright: Reprinted
author:  token的技术分享
originaltitle: （1）入门MasaFramework教程
originallink: https://www.cnblogs.com/hejiale010426/p/17209926.html
draft: false
cover: https://img1.dotnet9.com/2023/03/cover_09.png
categories: .NET
tags: MASA Framework
---

## 首先了解一下MasaFramework是什么?

MasaFramework是一个基于.Net6.0的后端框架, 可以被用于开发Web应用程序、WPF项目、控制台项目。

其实就是MasaFramework提供了很多功能的包，很强大，对于Dapr的支持非常好，如果有想尝试Dapr的可以试试MasaFramework。

## 然后我们开始使用MasaFramework，进入实战

1. 安装MasaFramework项目模板

```shell
dotnet new install Masa.Template
```

这样就安装成功了：

![](https://img1.dotnet9.com/2023/03/0901.png)

2. 创建项目

打开一个目录，打开控制台进行创建模板项目，创建一个mfDemo的项目模板

```shell
dotnet new masafx --name mfDemo
```

这样就创建完成了，打开解决方案

![](https://img1.dotnet9.com/2023/03/0902.png)

3. 项目结构解析

![](https://img1.dotnet9.com/2023/03/0903.png)

我们可以看到打开解决方案以后的项目结构。

项目区分`src/ApiGateways`，`src/Contracts`，`src/Services`，`src/Web`四层，这个时候可能就会有很多人有疑惑了，为什么跟传统的Abp架构设计有些差异，其实这个就是MasaFramework的框架美妙之处。

`src/ApiGateways`中包含对外使用的接口实现（`站长注：客户端接口调用的封装`），相当于我可以直接将`src/ApiGateways`给`src/Web`的前端项目使用，这样的好处就是减少前端项目的依赖性，并且利于接口的快速对接。

`src/Contracts`中包含了基本的模型，和一些共享的东西，`src/Contracts`是纯粹的，没有任何依赖，所以`src/ApiGateways`和`src/Services`都会直接依赖`src/Contracts`，用于共享Model或其他东西。

`src/Services`中就是包含了具体业务和实现，并且包含`Host`，在`Application`中包含业务处理或事件处理：

![](https://img1.dotnet9.com/2023/03/0904.png)

如果你并未使用MiniApi的话应该出现的就是Controllers：

![](https://img1.dotnet9.com/2023/03/0905.png)

其实建议使用MiniApi，因为两个实现方式不一样，导致MiniApi在性能上对比Controllers更好。

Infrastructure中就是项目的基础设施了，看图我们发现Entity和Middleware，Repository，DbContext都在基础设施中：

![](https://img1.dotnet9.com/2023/03/0906.png)

MasaFramework的设计就是简化项目复杂，将其揉合在一个项目中，如果你刚刚使用MasaFramework，千万千万不要拆分，你拆分了和MasaFramework本身设计就不太相符合，但是如果你是熟练的大佬，当我没说，刚刚入门MasaFramework请务必使用本身框架的设计。

`src/Web`就是我们的实际的前端项目了。

创建的默认的模板提供是Blazor Server模式的项目，可以自行拆分成三层项目`mfDemo.Shared`，`mfDemo.Server`，`mfDemo.WebAssembly`三层项目架构。

mfDemo.Shared可以理解成项目的所有实现和界面文件组织等一切功能。

mfDemo.Server其实就是个Blazor Server的壳，用于托管mfDemo.Shared项目。

mfDemo.WebAssembly其实也是个Blazor WebAssembly的壳，用于托管mfDemo.Shared项目。

这样我们的项目就可以支持Blazor Server和Blazor WebAssembly两种模式了，

## 结尾

通过上文我们可以基本将MasaFramework的项目结构了解清楚，也知道MasaFramework的设计了。

当前是MasaFramework的第一篇入门，我会继续学习MasaFramework并且分享给大家。

来自token的分享

[MASA Framework](https://docs.masastack.com/framework/getting-started/overview)

学习交流：737776595