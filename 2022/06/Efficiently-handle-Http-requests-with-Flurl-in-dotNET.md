---
title: 在 .NET 中使用 Flurl 高效处理Http请求
slug: Efficiently-handle-Http-requests-with-Flurl-in-dotNET
description: Flurl是一个现代的，流利的，支持异步的，可测试的，可移植的，URL增强和Http客户端组件。
date: 2022-06-24 21:45:29
copyright: Reprint
author: SpringLeee
originaltitle: 在 .NET 中使用 Flurl 高效处理Http请求
originallink: https://www.cnblogs.com/myshowtime/p/14512563.html
draft: False
cover: https://img1.dotnet9.com/2022/06/cover_17.jpg
categories: .NET相关
---

## 简介

官方介绍，Flurl是一个现代的，流利的，支持异步的，可测试的，可移植的，URL增强和Http客户端组件。

## Url构建

现在有一个登录的接口，地址如下：

```shell
https://www.some-api.com/login?name=Lee&pwd=123456
```

我们在处理这个地址的时候，会拼接 login, 然后拼接`?`号, 然后拼接参数，中间还要拼接`&` 得到最终的地址。

使用 Flurl 构建，首先需要通过 Nuget 安装 `Flurl` 组件。

```C#
var url = "http://www.some-api.com"
          .AppendPathSegment("login")
          .SetQueryParams(new
          {
              name = "Lee",
              pwd = "123456" 
          });  
```

这很简单，这是最简单的Get请求，同样的我们也可以使用 Uri 的扩展方法

```C#
var url = new Uri("http://www.some-api.com").AppendPathSegment(...
```

## Http 增强

Flurl 是模块化的，所以还需要安装 `Flurl.Http`

```C#
using Flurl;
using Flurl.Http;

var result = await "http://www.some-api.com".AppendPathSegment("login").GetAsync();
```

上面的代码会发送一个GET请求，并返回一个`IFlurlResponse`，可以得到 StatusCode，Headers等，也可以通过 GetStringAsync 和 GetJsonAsync 得到响应内容。

如果只是想获取响应内容，我们看看 Flurl 有多简单：

```C#
T poco = await "http://api.foo.com".GetJsonAsync<T>();
string text = await "http://site.com/readme.txt".GetStringAsync();
byte[] bytes = await "http://site.com/image.jpg".GetBytesAsync();
Stream stream = await "http://site.com/music.mp3".GetStreamAsync();
```

**Post提交**

```C#
await "http://api.foo.com".PostJsonAsync(new { a = 1, b = 2 });
```

**动态类型 dynamic**

```C#
dynamic d = await "http://api.foo.com".GetJsonAsync();
```

**设置请求标头：**

```C#
await url.WithHeader("Accept", "text/plain").GetJsonAsync();

await url.WithHeaders(new { Accept = "text/plain", User_Agent = "Flurl" }).GetJsonAsync();
```

**基础身份验证**

```C#
await url.WithBasicAuth("username", "password").GetJsonAsync();
```

**OAuth 2.0**

```C#
await url.WithOAuthBearerToken("mytoken").GetJsonAsync();
```

**表单提交**

```C#
await "http://site.com/login".PostUrlEncodedAsync(new { 
    user = "user", 
    pass = "pass"
});
```

## HttpClient 管理

我们通常不会创建太多的 HttpClient, 过多的连接会耗尽服务器资源，通常会抛出 SocketException 异常，大部分还是使用 HttpClientFactory。

在 Flurl 库中，它是内部管理 HttpClient实例, 通常一个主机Host，会创建一个HttpClient，然后缓存来复用。

Flurl 也很好的支持了IOC容器，你也可以在依赖注入中使用它。

## 总结

Flurl 组件让Http操作变得更简单易用，你可以在项目中尝试使用它，其他的还有一些功能，可测试可配置等，你都可以在官网找到它的文档。

欢迎扫码关注我们的公众号 【半栈程序员】，专注国外优秀博客的翻译和开源项目分享。

