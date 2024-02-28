---
title: 微服务 配置中心 Nacos .Net 5
slug: Micro-service-configuration-center-Nacos-dotNet-5
description: 基于Nacos来一篇关于微服务的配置中心方案Demo。
date: 2022-05-17 22:14:36
copyright: Reprinted
author: 蓝创精英团队
originaltitle: 微服务 配置中心 Nacos .Net 5
originallink: https://blog.csdn.net/i2blue/article/details/124827269
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_46.jpeg
categories: .NET
tags: Web API
---

基于Nacos来一篇关于微服务的配置中心方案Demo。

Nacos是开源的，同时，阿里云也有收费的关于它的服务，公司刚好是依托阿里云的服务体系，所以，使用它作为配置中心的可能性还是很大的，所以，基于它，来了一个示例。

## 1. 环境如何搭建

它的环境相对还是比较复杂的，需要有Docker服务和测试的Demo服务，以及它还需要相应的Mysql数据库：

 1. Docker 提供Nacos服务
 2. WebDemo
 3. Mysql需要的指定数据库

## 2. 获取官网的表结构
C#的官方示例地址是:https://github.com/nacos-group/nacos-sdk-csharp

官方提供的地址在这里 ：https://github.com/alibaba/nacos.git

SQL 会在 nacos\distribution\conf\nacos-mysql.sql 

我这边项目里会提供需要的sql。

![](https://img1.dotnet9.com/2022/05/4601.png)

我这边插入指定的脚本，就OK，前提是你要有这个库。

最后看到会有以下这些表

![](https://img1.dotnet9.com/2022/05/4602.png)


## 3. 启动Docker服务

我这边默认是使用Docker Desktop，直接输入命令就搞定了。

如果你也使用这种Docker，那么，你可以参考我之前关于Docker相关的文章即可.

```shell
docker run --name nacos  -d -p 8848:8848 ^
-e MODE=standalone ^
-e MYSQL_SERVICE_HOST=192.168.1.8 ^
-e MYSQL_SERVICE_DB_NAME=nacos_config ^
-e MYSQL_SERVICE_PORT=3306 ^
-e MYSQL_SERVICE_USER=root ^
-e MYSQL_SERVICE_PASSWORD=123456 ^
nacos/nacos-server
```

如何判断服务是否OK

可以游览器访问   http://localhost:8848/nacos/#/login 地址

![](https://img1.dotnet9.com/2022/05/4603.png)

这样的话，我们就可以登录平台上看看有啥子了

初始的用户名和密码都是 **nacos**

## 4. 增加相应的配置信息

![](https://img1.dotnet9.com/2022/05/4604.png)

 1. 第一，就是我们要增加的配置菜单
 2. 第二，就是相应的命名空间
 3. 第三，就是需要的具体的配置

其中我新增了一个Test的命名空间

![](https://img1.dotnet9.com/2022/05/4605.png)

然后，在每个里面增加了两个配置如下

![](https://img1.dotnet9.com/2022/05/4606.png)

![](https://img1.dotnet9.com/2022/05/4607.png)

## 5. 新建一个WebAPi项目

新建一个默认的webapi项目，然后引入以下nuget包即可

```csharp
nacos-sdk-csharp.AspNetCore
```

另外需要修改默认Program这个地方为以下配置

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .ConfigureAppConfiguration((context, builder) =>
        {
            var c = builder.Build();

            // read configuration from config files
            // it will use default json parser to parse the configuration store in nacos server.
            builder.AddNacosV2Configuration(c.GetSection("NacosConfig"));
            // you also can specify ini or yaml parser as well.
            // builder.AddNacosV2Configuration(c.GetSection("NacosConfig"), Nacos.IniParser.IniConfigurationStringParser.Instance);
            // builder.AddNacosV2Configuration(c.GetSection("NacosConfig"), Nacos.YamlParser.YamlConfigurationStringParser.Instance);
        });
```

还需要注意的就是 最重要的配置文件(appsettings.json)了如下:

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*",
  "NacosConfig": {
    "Listeners": [
      {
        "Optional": false,
        "DataId": "conn",
        "Group": "DEFAULT_GROUP"
      },
      {
        "Optional": false,
        "DataId": "other",
        "Group": "DEFAULT_GROUP"
      }
    ],
    "Tenant": "1806893a-7997-4657-9325-d4294fbf0f4a",
    "ServerAddresses": [ "http://192.168.1.8:8848/" ],
    "UserName": "nacos",
    "Password": "nacos",
    "ConfigUseRpc": false,
    "NamingUseRpc": false
  }
}
```

其中 Tenant为指定配置中心命名空间的ID，另外就是Listeners的是这个命名空间下的配置的Data Id。

必须要有 ConfigUseRpc和NamingUseRpc这2个参数，若用的是http协议，则都是false ,若用grpc协议则为true.（不写会报错）

为了增加演示效果，我这里修改了默认的控制器方法为读取指定的配置

```csharp
private readonly IConfiguration _configuration;
public HomeController(ILogger<HomeController> logger, IConfiguration configuration)
{
    _logger = logger;
    _configuration = configuration;
}

public IActionResult Index(string key)
{
    if (string.IsNullOrWhiteSpace(key))
    {
        return Content("key is empty!");
    }
    return Content(_configuration[key]);
}

```
### 启动后效果
访问 http://localhost:38889/home?key=mysql 地址如下

![](https://img1.dotnet9.com/2022/05/4608.png)

访问 http://localhost:38889/home?key=other 地址如下

![](https://img1.dotnet9.com/2022/05/4609.png)

访问 http://localhost:38889/home?key=redis 地址如下

![](https://img1.dotnet9.com/2022/05/4610.png)

可见已经能全部查到相应的配置信息了

这个时候，我动态修改 Nacos上的配置信息

![](https://img1.dotnet9.com/2022/05/4611.png)

然后，在查询一下看看是不是最新的

发现已经是最新的，并且，控制台也直接更新为最新的配置了

![](https://img1.dotnet9.com/2022/05/4612.png)

![](https://img1.dotnet9.com/2022/05/4613.png)

可见这个配置中心也挺好用的

## 6. 最后奉上github地址

- github :https://github.com/kesshei/NacosConfigDemo.git
- gitee : https://gitee.com/kesshei/NacosConfigDemo.git
