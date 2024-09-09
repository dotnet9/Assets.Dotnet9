---
title: EasyNetQ-用于使用 RabbitMQ 的 .NET API开源的工具库
slug: EasyNetQ-Open-source-tool-library-for-using-RabbitMQ-dotNET-API
description: EasyNetQ 的目标是提供一个库，用于在 .NET 中使用 RabbitMQ 尽可能简单。
date: 2022-07-26 06:32:24
copyright: Reprinted
author: 黑哥聊dotNet
originalTitle: EasyNetQ-用于使用 RabbitMQ 的 .NET API开源的工具库
originalLink: https://mp.weixin.qq.com/s/hBDvEe9E8k2M0cv_8Y9h2w
draft: False
cover: https://img1.dotnet9.com/2022/07/cover_24.png
categories: 
    - .NET
tags: 
    - .NET
---

## Part1 介绍

![](https://img1.dotnet9.com/2022/07/2403.png)

`EasyNetQ` 是用于[RabbitMQ](http://www.rabbitmq.com/)的简单易用 .NET API 。

如果您只想尽快启动并运行，请转到[快速入门](https://github.com/EasyNetQ/EasyNetQ/wiki/Quick-Start)指南。

`EasyNetQ` 的目标是提供一个库，用于在 `.NET` 中使用 `RabbitMQ` 尽可能简单。为了做到这一点，它通过强制执行一些简单的约定来以灵活性换取简单性。这些包括：

- 消息应该由 `.NET` 类型表示。
- 消息应按其 `.NET` 类型进行路由。

这意味着消息是由 `.NET` 类定义的。您要发送的每种不同的消息类型都由一个类表示。该类必须是公共的，必须具有默认构造函数和公共`读/写`属性。您通常不会在消息中实现任何功能，而是将其视为简单的数据容器或数据传输对象 (`DTO`)。这是一个简单的消息：

```csharp
public class MyMessage
{
    public string Text { get; set; }
}
```

`EasyNetQ` 按消息类型路由消息。 当您发布消息时，`EasyNetQ` 会检查其类型并根据类型名称、命名空间和程序集为其提供`路由键`(routing key)。 在消费端，订阅者订阅一个类型。 订阅一个类型后，该类型的消息会被路由到订阅者。

默认情况下，`EasyNetQ` 使用 `Newtonsoft.Json` 库将 `.NET` 类型序列化为 `JSON`。 这样做的好处是消息是人类可读的，因此您可以使用 `RabbitMQ` 管理应用程序等工具来调试消息问题。

## Part2 API 设计

![](https://img1.dotnet9.com/2022/07/2401.png)

`EasyNetQ` 是在 `RabbitMQ.Client` 库之上提供服务的组件集合。它们执行序列化、错误处理、线程编组、连接管理等操作。它们由 `mini-IoC` 容器组成。您可以很容易地用您自己的实现替换任何组件。因此，如果您想要 `XML 序列化`而不是内置 `JSON`，只需编写 `ISerializer` 的实现并将其注册到容器中。

这些组件以 `IAdvancedBus API` 为前端。这看起来很像 `AMQP` 规范，实际上您可以从这个 `API` 运行大多数 `AMQP` 方法。此 `API` 对您隐藏的唯一 `AMQP` 概念是通道。这是因为通道是一个令人困惑的低级概念，一开始就不应该成为 `AMQP` 规范的一部分。老实说，“高级”对于这个 `API` 来说并不是一个很好的名字，`Iamqp`会好得多。

位于高级 `API` 之上的是一组消息传递模式：`发布/订阅`、`请求/响应`和`发送/接收`。这是 `EasyNetQ` 的“主见”部分。这是我们对如何实施这些模式的看法。灵活性很小；要么你接受我们的做事方式，要么你不使用它。目的是您，用户，不必花费脑力去重新发明相同的模式；您不必在每次只想发布和订阅消息时都做出选择。它旨在实现 `EasyNetQ` 的核心目标，即尽可能轻松地使用 `RabbitMQ`。

这些模式位于 `IBus` API 后面。再一次，这是一个糟糕的名字，它与消息总线的概念几乎没有关系。更好的名称是 `IPackagedMessagePatterns`。

`IBus` 旨在为 80% 的用户、80% 的时间工作。这并不详尽。如果您要实现的模式不是由 `IBus` 提供的，那么您应该使用 `IAdvancedBus`。这样做没有问题，`EasyNetQ` 就是这样设计的。

## Part3 为什么我需要 EasyNetQ？

`RabbitMQ` 不是已经有 `.NET` 客户端了吗？

是的，它确实。您可以在[此处](http://www.rabbitmq.com/dotnet.html)下载 .NET AMQP 客户端库。

那么为什么我需要 `EasyNetQ`？`RabbitMQ` .NET 客户端实现了 `AMQP` 协议的客户端（而 `RabbitMQ` 实现了服务器端）。`AMQP` 旨在作为消息传递的 `HTTP`。它被设计成跨平台和语言无关的。它还旨在灵活地支持基于 `Exchange/Binding/Queue` 模型的各种消息传递模式。

拥有这种灵活性很棒，但随着灵活性而来的是复杂性。这意味着您需要编写大量代码才能实现 `RabbitMQ` 客户端。通常，此代码将包括：

- 实现消息传递模式，例如发布/订阅或请求/响应。虽然，公平地说，.NET 客户端确实在这里提供了一些支持。
- 实现路由策略。您将如何设计交换队列绑定，以及如何在生产者和消费者之间路由消息？- 实现消息序列化/反序列化。您将如何将 AMQP 中消息的二进制表示转换为您的编程语言可以理解的内容？
- 为订阅实现消费者线程。您需要有一个专门的消费者循环来等待您订阅的消息。您将如何处理多个订阅者或临时订阅者，例如等待请求响应的订阅者？
- 实现订阅者重新连接。如果连接中断或 RabbitMQ 服务器弹跳，您如何检测它并确保重建所有订阅？
- 了解并实施服务质量设置。您需要进行哪些设置以确保您拥有可靠的客户端。
- 实现错误处理策略。如果您的客户端收到格式错误的消息，或者抛出意外异常，您应该怎么做？
- 实现发布者确认可靠消息传递。

EasyNetQ 旨在将所有这些问题封装在一个简单易用的库中，该库位于现有 AMQP 客户端之上。它是 RabbitMQ 在大容量商业环境中几年使用经验的结晶。

## Part4 简单使用

接到 RabbitMQ 代理

```csharp
var bus = RabbitHutch.CreateBus("host=localhost");
```

发布消息

```csharp
await bus.PubSub.PublishAsync(message);
```

发布一条延迟 5 秒的消息

```csharp
await bus.Scheduler.FuturePublishAsync(message, TimeSpan.FromSeconds(5));
```

订阅消息

```csharp
await bus.PubSub.SubscribeAsync<MyMessage>(
    "my_subscription_id", msg => Console.WriteLine(msg.Text)
);
```

RPC 服务器

```csharp
await bus.Rpc.RespondAsync<TestRequestMessage, TestResponseMessage>(request =>
    new TestResponseMessage{ Text = request.Text + " all done!" }
);
```

## Part5 地址

- 原文链接：https://github.com/EasyNetQ/EasyNetQ/wiki/Introduction
- 仓库链接：https://github.com/EasyNetQ/EasyNetQ

![](https://img1.dotnet9.com/2022/07/2402.png)
