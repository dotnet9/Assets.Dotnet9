---
title: （2）MasaFramework入门第二篇，安装MasaFramework了解各个模板
slug: Getting-Started-with-MasaFramework-Part-2-Installing-MasaFramework-Understanding-Various-Templates
description: 安装MasaFramework了解各个模板
date: 2023-03-25 19:43:47
copyright: Reprinted
author: token的技术分享
originalTitle: （2）MasaFramework入门第二篇，安装MasaFramework了解各个模板
originalLink: https://www.cnblogs.com/hejiale010426/p/17223279.html
draft: false
cover: https://img1.dotnet9.com/2023/03/cover_09.png
categories: 
    - .NET
tags: 
    - MASA Framework
---

## 安装 MasaFramework 模板

执行以下命令安装最新 Masa 的模板

```shell
dotnet new install Masa.Template
```

安装完成将出现四个模板

![](https://img1.dotnet9.com/2023/03/1401.png)

### Masa Blazor App

Masa Blazor App 的模板创建的是一个没有携带解决方案的项目模板，默认项目结构如图：

![](https://img1.dotnet9.com/2023/03/1402.png)

一个简单的 Masa Blazor Server 项目。

### Masa Blazor Pro Web

Masa Blazor Pro Web 的模板创建类型有多种：

![](https://img1.dotnet9.com/2023/03/1403.png)

- Wasm 就是单纯的 Wasm 模式。
- Wasm-Host 就是启动一个 Server 托管 Wasm。
- Wasm-PWA 支持浏览器安装。
- Server 就是单纯的 Blazor Server 模式。
- ServerAndWasm 是提供一个 razor 类库作为界面，支持 Blazor Server 和 Blazor Wasm 俩种模式。

对于上面五种模式更推荐第五种模式，这样就可以在部署的时候部署 Blazor Server 和 Blazor Wasm 两种模式，可让用户自行切换，剖析以下 Masa Blazor Pro Web 的项目结构：

![](https://img1.dotnet9.com/2023/03/1404.png)

MasaWebPro1 项目就是 Razor 类库，提供界面逻辑和实际业务。

MasaWebPro1.Server 项目就只是以 Blazor Server 模式托管 MasaWebPro1 项目的界面。

MasaWebPro1.WebAssembly 项目就只是以 Blazor WebAssembly 模式托管 MasaWebPro1 项目的界面。

运行项目将得到一个精美的项目模板：

![](https://img1.dotnet9.com/2023/03/1405.png)

可对其修改进行二次开发，也可以将 Pro 和 MasaFramework 结合一块使用。

### Masa Blazor Website

Masa Blazor Website 项目结构：

![](https://img1.dotnet9.com/2023/03/1406.png)

Masa Blazor Website 算是老版本的文档站点的模板，简单描述一下，默认使用了全球化。

### Masa Framework Project

Masa Framework Project 就是我们的主角了。

需要使用 MasaFramework 的同志们就需要创建这个模板了，之前的模板都是单纯的 Blazor。

当我们创建 MasaFramework 的时候存在多个选项：

![](https://img1.dotnet9.com/2023/03/1407.png)

- Use Controllers：使用控制器启用以后不使用 MiniApis（更推荐使用 MiniApis）
- Enable OpenAPI Support： 其实是否默认使用 Swagger
- Add Dapr Support ：添加 Dapr 的支持
- Use Dapr Actor ：使用 Dapr Actor
- Add Authorization An Authentication：添加授权和认证
- Add Fluent Validation Middleware：添加校验中间件

分别讲解一下 Choice Add Service Project and Mode 的 Basic ，Cqrs，Ddd，Cqrs&Ddd 四个项目模板，Choice Add Web Project 其实就是 Blazor 的托管模式：

#### Basic：

![](https://img1.dotnet9.com/2023/03/1408.png)

一个最基本的 MasaFramework 的项目结构。

#### Cqrs：

![](https://img1.dotnet9.com/2023/03/1409.png)

MasaFramework 的 Cqrs 结构，对比基本的 MasaFramework 项目来说有些差异的。

> 小知识：CQRS（Command Query Responsibility Segregation）是一种架构模式，它将读操作和写操作分离。CQRS 的基本思想是将读操作和写操作分离，这样可以优化系统性能和可扩展性。CQRS 还提供了一种简单的方法来实现事件驱动架构（Event-Driven Architecture），这种架构可以让系统更加灵活和可扩展。

#### Ddd：

![](https://img1.dotnet9.com/2023/03/1410.png)

MasaFramework 的 Ddd 项目和基本模板的差异也很明显。

> 小知识：DDD（Domain Driven Design）是一种软件开发方法论，它强调在设计应用程序时，要将业务领域的概念和业务规则放在首位，而不是技术实现。DDD 的目标是让开发人员更好地理解和实现复杂的业务需求。

#### Cqrs&Ddd：

![](https://img1.dotnet9.com/2023/03/1411.png)

Cqrs&Ddd 集成了 Cqrs 和 Ddd 俩个项目模板的特性，是一个稍微复杂的框架。

> 小知识：DDD 和 CQRS 通常一起使用，因为它们的目标是相同的：将业务领域的概念和业务规则放在首位，并将技术实现放在其次。在使用 DDD 和 CQRS 时，开发人员通常会将业务逻辑和数据访问逻辑分离，这样可以更好地管理代码和维护系统。
>
> DDD 和 CQRS 是两种不同的方法论，但它们都强调将业务需求放在首位，并将技术实现放在其次。如果您正在开发复杂的应用程序，那么使用 DDD 和 CQRS 可能会对您有所帮助。

## 项目使用

如果你想使用 MasaFramework 的话，可以将 Masa Pro 的模板和 MasaFramework 的模板结合起来一块使用：

![](https://img1.dotnet9.com/2023/03/1412.png)

这个是我目前使用到 MasaFramework 的项目，Web 是将 Pro 的模板嵌入进来，并进行修改，当前项目还在完善，这也是我第一个接触 MasaFramework 实践的项目，因为符合我需要的，体积小，依赖少。

## 结尾

来着 token 的分享

技术交流群：737776595

MasaFramework 学习地址：[MASA Framework](https://docs.masastack.com/framework/getting-started/overview)
