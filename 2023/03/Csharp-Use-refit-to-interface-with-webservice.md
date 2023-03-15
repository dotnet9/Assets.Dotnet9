---
title:  C#使用Refit对接WebService接口
slug: Csharp-Use-refit-to-interface-with-webservice
description: 群友说.NET Core无法对接WebService，站长找了些资料，希望能帮助到他
date: 2023-03-15 20:33:14
copyright: Default
draft: false
cover: https://img1.dotnet9.com/2023/03/refit_logo.png
categories: .NET相关
tags: Refit,WebService
---

Refit 是一款强大的类型安全的 RESTful HTTP 客户端库，它能够帮助我们轻松地与 Web API 进行通信。不过在本问题中，我们需要使用 Refit 与 Web Service 进行通信，因此需要对 Refit 进行一些特定的配置。

下面是一个使用 Refit 调用 Web Service 接口的示例：

1. 首先，需要在项目中添加 Refit 库的引用，可以通过 NuGet 包管理器搜索 Refit 并安装。

2. 然后，我们需要定义一个接口来描述 Web Service 接口，例如：

```
public interface IMyWebService
{
    [Post("/MyWebService.asmx")]
    Task<string> MyWebServiceMethod(string param1, string param2);
}
```

其中 `[Post]` 指定了调用的 HTTP 方法和 URL，`Task<string>` 是方法返回类型。

3. 接下来，我们需要使用 Refit 的 `RestService.For` 方法创建一个客户端实例：

```
var client = RestService.For<IMyWebService>("http://example.com");
```

其中 `"http://example.com"` 是 Web Service 的地址。

4. 最后，我们就可以使用客户端实例来调用 Web Service 接口了：

```
var result = await client.MyWebServiceMethod("param1", "param2");
```

其中 `result` 是 Web Service 方法的返回值。

需要注意的是，由于 Web Service 接口不是基于 RESTful 架构的，因此需要进行一些特定的配置。例如，在接口定义中使用 `[Post]` 指定调用的 HTTP 方法为 POST，同时需要将 Web Service 方法的名称作为 URL 的一部分，例如：

```
public interface IMyWebService
{
    [Post("/MyWebService.asmx/MyWebServiceMethod")]
    Task<string> MyWebServiceMethod(string param1, string param2);
}
```

另外，需要在客户端实例中指定 Web Service 的 SOAP 1.1 命名空间，例如：

```
var client = RestService.For<IMyWebService>("http://example.com", new RefitSettings
{
    UrlParameterFormatter = new SoapUrlParameterFormatter(),
    ContentSerializer = new XmlContentSerializer(new RefitXmlSerializerSettings
    {
        Namespace = "http://schemas.xmlsoap.org/soap/envelope/",
        UseXmlSerializerFormat = true
    })
});
```

在这里，我们使用了 `SoapUrlParameterFormatter` 来处理 URL 中的参数，使用了 `XmlContentSerializer` 和 `RefitXmlSerializerSettings` 来处理请求和响应的 XML 数据。

总之，使用 Refit 调用 Web Service 接口需要进行一些特定的配置，但是只要按照上述示例进行操作，就可以轻松地完成对接。