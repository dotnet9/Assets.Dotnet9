---
title: gRPC入门与实操(.NET篇)
slug: GRPC-Introduction-and-Practice-dotnet
description: 长久以来，我们在前后端交互时使用WebApi + JSON方式，后端服务之间调用同样如此
date: 2023-01-11 20:47:14
copyright: Reprinted
author: 莱布尼茨
originalTitle: gRPC入门与实操(.NET篇)
originalLink: https://www.cnblogs.com/newton/archive/2023/01/10/17033789.html
draft: false
cover: https://img1.dotnet9.com/2023/01/cover_02.png
categories: 
    - .NET
tags: 
    - .NET
---

## 1. 为什么选择 gRPC

### 1.1. 历史

长久以来，我们在前后端交互时使用`WebApi + JSON`方式，后端服务之间调用同样如此（或者更久远之前的`WCF + XML`方式）。WebApi + JSON 是优选的，很重要的一点是它们两者都是平台无关的三方标准，且足够语义化，便于程序员使用，在异构（前后端、多语言后端）交互场景下是不二选择。然而，在后端服务体系改进特别是后来微服务兴起后，我们发现，前后端交互理所当然认可的 WebApi + JSON 在后端体系内显得有点不太合适：

1. JSON 字符编码方式使得传输数据量较大，而后端一般并不需要直接操作 JSON，都会将 JSON 转为平台专有类型后再处理；既然需要转换，为什么不选择一个数据量更小，转换更方便的格式呢？
2. 调用双方要事先约定数据结构和调用接口，稍有变动就要手动更新相关代码（Model 类和方法签名）；是否可以将约定固化为文档，服务提供者维护该文档，调用方根据该文档可以方便地生成自己需要的代码，在文档变化时代码也可以自动更新？
3. [之前] WebApi 基于的 Http[1.1] 协议已经诞生 20 多年，其定义的交互模式在今日已经捉襟见肘；业界需要一个更有效率的协议。

### 1.2. 高效传输-Http2.0

我们先来说第 3 个问题，其实很多大厂内部早已开始着手处理，并诞生了一些应用广泛的框架，如阿里开源的`Dubbo`，直接抛弃了 Http 改为基于 Tcp 实现，效率得到明显提升，不过 Dubbo 依赖 Java 环境，无法跨平台使用，不在我们考虑范围。

另一个大厂 Google，内部也在长期使用自研的`Stubby`框架，与 Dubbo 不同的是，Studdy 是跨平台的，但是 Google 认为 Studdy 不基于任何标准，而且与其内部基础设施紧密耦合，并不适合公开发布。

同时 Google 也在对 Http1.1 协议进行增强，该项目是 2012 年提出的 SPDY 方案，其优化了 Http 协议层，新增的功能包括数据流的多路复用、请求优先级以及 HTTP 报头压缩。Google 表示，引入 SPDY 协议后，在实验室测试中页面加载速度比原先快 64%。巨大的提升让大家开始从正面看待和解决老版本 Http 协议的问题，这也直接加速了 Http2.0 的诞生。实际上，Http2.0 是以 SPDY 为原型进行讨论和标准化的，当然也做了更多的改进和调整。

随着 Http2.0 的出现和普及，许多与 Stubby 相同的功能已经出现在公共标准中，包括 Stubby 未提供的其他功能。很明显，是时候重做 Stubby 以利用这种标准化，并将其适用范围扩展到分布式计算的最后一英里，支持移动设备(如安卓)、物联网(IOT)、和浏览器连接到后端服务。

2015 年 3 月，Google 决定在公开场合构建下一版 Stubby，以便与业界分享经验，并进行相关合作，也就是本文的主角`gRPC`。

### 1.3. 高效编码-protobuf

回头来看第 1 个问题，解决起来相对比较简单，无非是将傻瓜式字符编码转为更有效的二进制编码（比如数字 10000 JSON 编码后是 5 个字节，按整型编码就是 4 个字节），同时加上些事先约定的编码算法使得最终结果更紧凑。常见的平台无关的编码格式有`MessagePack`和`protobuf`等，我们以 protobuf 为例。

protobuf 采用 `varint` 和 处理负数的 `ZigZag` 两种编码方式使得数值字段占用空间大大减少；同时它约定了字段类型和标识，采用 `TLV` 方式，将字段名映射为小范围结果集中的一项（比如对于不超过 256 个字段的数据体来说，不管字段名本身的长度多少，每个字段名都只要 1 个字节就能标识），同时移除了分隔符，并且可以过滤空字段（若字段没有被赋值，那么该字段不会出现在序列化结果中）。

### 1.4. 高效编程-代码生成工具

第 2 个问题呢，其实需要的就是[每个平台]一套代码生成工具。生成的代码需要覆盖类的定义、对象的序列化/反序列化、服务接口的暴露和远程调用等等必要的模板代码，如此，开发人员只需要负责接口文档的维护和业务代码的实现（很自然的面向接口编程：））。此时，采用 protobuf 的`gRPC`自然而然的映入眼帘，因为对于目前所有主要的编程语言和平台，都有 gRPC 工具和库，包括 .NET、Java、Python、Go、C++、Node.js、Swift、Dart、Ruby 以及 PHP。可以说，这些工具和库的提供，使得 gRPC 可以跨多种语言和平台一致地工作，成为一个全面的 RPC 解决方案。

## 2. gRPC 在 .NET 中的使用

ASP.NET Core 3.0 开始，支持`gRPC`作为 .NET 平台中的“一等公民”。

### 2.1. 服务端

在 VS 中新建`ASP.NET Core gRPC 服务`，会发现在项目文件中自动引入了`Microsoft.NET.Sdk.Web`类库，很明显，gRPC 服务仍然是 Web 服务，毕竟它走的是 Http 协议。同时还引入了`Grpc.AspNetCore`类库，该类库引用了几个子类库需要了解下：

- `Google.Protobuf`：包含 protobuf 预定义 message 类型在 C# 中的实现；
- `Grpc.Tools`：上面讲到的代码生成工具，编译时使用，运行时不需要，因此依赖项标记为 PrivateAssets="All"；
- `Grpc.AspNetCore.Server`：服务端专用；
- `Grpc.Net.ClientFactory`：客户端专用，如果只是提供服务的话，那么该类库可以移除。

定义接口文件：

```csharp
syntax = "proto3";

// 指定自动生成的类所在的命名空间，如果不指定则以下面的 package 为命名空间，这主要便于本项目内部的模块划分
option csharp_namespace = "Demo.Grpc";

// 对外提供服务的命名空间
package TestDemo;

// 服务
service Greeter {
  // 接口
  rpc SayHello (HelloRequest) returns (HelloReply);
}

// 不太好的一点是就算只有一个基础类型字段，也要新建一个 message 进行包装
message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

然后把它包含到项目文件中：

```xml
<ItemGroup>
  <Protobuf Include="Protos\greeter.proto" GrpcServices="Server" />
</ItemGroup>
```

编译一下，Grpc.Tools 将帮我们生成 GreeterBase 类及两个模型类:

```csharp
public abstract partial class GreeterBase
{
    public virtual Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        throw new RpcException(new Status(StatusCode.Unimplemented, ""));
    }
}

public class HelloRequest
{
    public string Name { get; set; }
}

public class HelloReply
{
    public string Message { get; set; }
}
```

这里的 SayHello 是个空实现，我们新建一个实现类并填充业务逻辑，比如：

```csharp
public class GreeterService : GreeterBase
{
    public override Task<HelloReply> SayHello(HelloRequest request, ServerCallContext context)
    {
        return Task.FromResult(new HelloReply { Message = $"Hello {request.Name}" });
    }
}
```

最后将服务添加到路由管道，对外暴露：

```csharp
using Demo.Grpc.Services;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddGrpc();

var app = builder.Build();

// Configure the HTTP request pipeline.
app.MapGrpcService<GreeterService>();

app.Run();
```

#### 2.1.1. protobuf-net.Grpc

如果觉得写 .proto 文件太别扭，希望可以按传统方式写接口，那么社区项目`protobuf-net.Grpc`值得尝试，使用它可以它通过特性批注的 .NET 类型来定义应用的 gRPC 服务和消息。

首先我们不需要再引用 Grpc.AspNetCore，而是改为引用 protobuf-net.Grpc 库。同样也不需要写 .proto 文件，而是直接写接口类：

```csharp
using ProtoBuf.Grpc;
using System.Runtime.Serialization;
using System.ServiceModel;
using System.Threading.Tasks;

namespace Demo.Grpc;

[DataContract]
public class HelloReply
{
    [DataMember(Order = 1)]
    public string Message { get; set; }
}

[DataContract]
public class HelloRequest
{
    [DataMember(Order = 1)]
    public string Name { get; set; }
}

[ServiceContract(Name = "TestDemo.GreeterService")]
public interface IGreeterService
{
    [OperationContract]
    Task<HelloReply> SayHelloAsync(HelloRequest request, CallContext context = default);
}
```

注意其中特性的修饰。

写完实现类后，在 Program.cs 中注册即可，此处不再赘述。

使用 protobuf-net.Grpc，我们不需要写 .proto 文件，但是调用方特别是其它平台的调用方，需要 .proto 文件来生成相应的客户端，难道我们还要另外再写一份吗？别急，我们可以引入`protobuf-net.Grpc.AspNetCore.Reflection`，它引用的`protobuf-net.Grpc.Reflection`提供了根据 C# 接口生成 .proto 文件的方法；同时使用它还便于客户端测试，同`Grpc.AspNetCore.Server.Reflection`的作用一样，下文会讲到。

#### 2.1.2. 异常处理

.Net 为 gRPC 提供了拦截器机制，可新建一个拦截器统一处理业务异常，比如：

```csharp
public class GrpcGlobalExceptionInterceptor : Interceptor
{
    private readonly ILogger<GrpcGlobalExceptionInterceptor> _logger;

    public GrpcGlobalExceptionInterceptor(ILogger<GrpcGlobalExceptionInterceptor> logger)
    {
        _logger = logger;
    }

    public override async Task<TResponse> UnaryServerHandler<TRequest, TResponse>(
        TRequest request,
        ServerCallContext context,
        UnaryServerMethod<TRequest, TResponse> continuation)
    {
        try
        {
            return await continuation(request, context);
        }
        catch (Exception ex)
        {
            _logger.LogError(new EventId(ex.HResult), ex, ex.Message);

            // do something

            // then you can choose throw the exception again
            throw ex;
        }
    }
}
```

上述代码在处理完异常后重新抛出，旨在让客户端接收处理该异常，然而，实际上客户端是无法接收到该异常信息的，除非服务端抛出的是`RpcException`；同时，为使客户端得到正确的 HttpStatusCode（默认是 200，即使客户端得到是 RpcException），需要显式给`HttpContext.Response.StatusCode`赋值，如下：

```csharp
// ...

catch(Exception ex)
{
    var httpContext = context.GetHttpContext();
    httpContext.Response.StatusCode = StatusCodes.Status400BadRequest;

    // 注意，RpcException 的 StatusCode 和 Http 的 StatusCode 不是一一对应的
    throw new RpcException(new Status(StatusCode.XXX, "some messages"));
}

// ...
```

我们可以在构造 RpcException 对象时传递`Metadata`，用于携带额外的数据到客户端，如果需要传递复杂对象，那么要先按约定序列化成字节数组。

拦截器逻辑完成后，需要在服务注入时设置如下：

```csharp
builder.Services.AddGrpc(options =>
{
    options.Interceptors.Add<GrpcGlobalExceptionInterceptor>();
});
```

#### 2.1.3. 测试

服务端完成后，如果要借助`Postman`或者`gRPCurl`测试，那么它们其实就是调用服务的客户端，要让它们事先知道服务约定信息，有两种方法：

1. 给它们提供 .proto 文件，这个很好理解，关于服务的所有信息就定义在 .proto 文件中；
2. 服务端暴露一个可以获取服务信息的接口。

如果要用方法 2，那么要先引入`Grpc.AspNetCore.Server.Reflection`类库，然后在 Program.cs 中注册接口：

```csharp
// ...
builder.Services.AddGrpcReflection();

var app = builder.Build();

// ...

IWebHostEnvironment env = app.Environment;

if (env.IsDevelopment())
{
    app.MapGrpcReflectionService();
}
```

### 2.2. 客户端

客户端不需要 Grpc.AspNetCore.Server，所以我们直接引用 Google.Protobuf、Grpc.Tools、Grpc.Net.ClientFactory。

将服务端提供的 .proto 文件添加到项目中，并在项目文件中包含：

```xml
<ItemGroup>
  <Protobuf Include="Protos\greeter.proto" GrpcServices="Client" />
</ItemGroup>
```

注意，如果只需要服务端提供的部分接口，那么 .proto 文件中只保留必要的接口即可，真正做到按需索取：）。

我们还可以更改 .proto 文件中 message 的字段名（只要不改动字段类型和顺序），不会影响服务的调用。这也直接反映了 protobuf 不是按字段名而是事先定义的字段标识编码的。

由此，假如我们有多个 .proto 文件，使用到了相同结构的 message，无所谓字段名是否相同，我们都可以将这些 message 抽离为单独的一个 .proto 文件，然后其它的 .proto 文件使用`import "Protos/xxx.proto";`引入它。

编译一下，然后在 Program.cs 中注册服务客户端：

```csharp
// .proto 文件中的 package
using TestDemo;

// 这里注入的服务是 Transient 模式
builder.Services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

如此，其它地方就可以愉快地使用客户端调用远程服务了。

同服务端一样，我们可以给客户端配置统一的拦截器。如果服务端返回上文提到的 RpcException，客户端得到后是直接抛出的（就像是本地异常），我们可以新建一个专门的异常拦截器处理 RpcException 异常。

```csharp
builder.Services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor<ExceptionInterceptor>();  // 默认创建一次，并在 GreeterClient 实例之间共享
    //.AddInterceptor<ExceptionInterceptor>(InterceptorScope.Client); // 每个 GreeterClient 实例拥有自己的拦截器
```

具体的异常处理逻辑就不举例了。提一下，通过 `RpcException.Trailers` 可以获取异常的 metadata 数据。

另外，对于异常处理来说，如果项目是普通的 ASP.NET Core Web 服务，那么使用原先的 `ActionFilterAttribute`、`IExceptionFilter`等拦截器也是一样的，因为既然运行时出现了异常，这两者肯定也能捕获到。

### 2.3. 进阶知识

本文未涉及的 .NET-gRPC 的进阶知识诸如`单元测试`、`服务调用中止`、`负载均衡`、`健康监控`等，以后有机会再与大家分享。其实这方面微软官方文档已经讲解得相当全面了，但也难以覆盖在实操过程中遇到的所有问题，所以有此文以飨读者，还望不吝指教。

## 3. 参考资料

- [HTTP 2.0 的前世今生](https://www.sohu.com/a/117358761_472885)
- [.NET 性能优化-是时候换个序列化协议了](https://www.cnblogs.com/InCerry/p/Dotnet-Perf-Opt-Serialization-Protocol.html)
- [MessagePack 简析](https://www.cnblogs.com/sunzhenchao/p/8448929.html)
- [Varint 编码](http://wjhsh.net/jacksu-tencent-p-3389843.html)
- [Protobuf 标量数据类型](https://learn.microsoft.com/zh-cn/dotnet/architecture/grpc-for-wcf-developers/protobuf-data-types)

> 本文来自转载。
>
> 作者：莱布尼茨
>
> 原文标题：gRPC 入门与实操(.NET 篇)
>
> 原文链接：https://www.cnblogs.com/newton/archive/2023/01/10/17033789.html
