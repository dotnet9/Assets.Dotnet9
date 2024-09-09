---
title: 10 微秒级别性能！C# 开发的离线IP地址库
slug: 10-microsecond-level-performance-Offline-IP-address-library-developed-by-Csharp
description: ip2region v2.0 - 是一个离线IP地址定位库和IP定位数据管理框架，10微秒级别的查询效率，提供了众多主流编程语言的 xdb 数据生成和查询客户端实现。
date: 2023-07-07 01:08:39
copyright: Reprinted
author: 工具箱
originalTitle: 10 微秒级别性能！C# 开发的离线IP地址库
originalLink: https://mp.weixin.qq.com/s/h3yG5tod1Cs-KNRZQo1xNA
draft: false
cover: https://img1.dotnet9.com/2023/07/cover_03.png
categories: 
    - .NET
tags: 
    - .NET
---

![](https://img1.dotnet9.com/2023/07/cover_03.png)

## Ip2region 是什么？

ip2region v2.0 - 是一个离线 IP 地址定位库和 IP 定位数据管理框架，10 微秒级别的查询效率，提供了众多主流编程语言的 xdb 数据生成和查询客户端实现。

## 应用场景

Ip2region 广泛应用在各种需要进行 IP 地址定位的场景，比如，可以用于检测和阻止来自某些地区或国家的 IP 地址的攻击行为，可以根据用户的 IP 地址定位到具体的地理位置。可以根据 IP 地址的地理位置信息进行数据分析和统计，比如统计某个地区的用户量等。

## 功能特性

### 1、标准化的数据格式

每个 ip 数据段的 region 信息都固定了格式：国家|区域|省份|城市|ISP，只有中国的数据绝大部分精确到了城市，其他国家部分数据只能定位到国家，后前的选项全部是 0。

### 2、数据去重和压缩

xdb 格式生成程序会自动去重和压缩部分数据，默认的全部 IP 数据，生成的 ip2region.xdb 数据库是 11MiB，随着数据的详细度增加数据库的大小也慢慢增大。

### 3、极速查询响应

即使是完全基于 xdb 文件的查询，单次查询响应时间在十微秒级别，可通过如下两种方式开启内存加速查询：

vIndex 索引缓存 ：使用固定的 512KiB 的内存空间缓存 vector index 数据，减少一次 IO 磁盘操作，保持平均查询效率稳定在 10-20 微秒之间。xdb 整个文件缓存：将整个 xdb 文件全部加载到内存，内存占用等同于 xdb 文件大小，无磁盘 IO 操作，保持微秒级别的查询效率。

### 4、IP 数据管理框架

v2.0 格式的 xdb 支持亿级别的 IP 数据段行数，region 信息也可以完全自定义，例如：你可以在 region 中追加特定业务需求的数据，例如：GPS 信息/国际统一地域信息编码/邮编等。也就是你完全可以使用 ip2region 来管理你自己的 IP 定位数据。

### 如何在 C# 中使用？

1. 安装 Nuget 包 IP2Region.Net。

```shell
Install-Package IP2Region.Net
```

2. 使用 API 查询, 非常简单！

```csharp
using IP2Region.Net.XDB;

Searcher searcher = new Searcher();
searcher.Search("IP地址");
```

**依赖注入**

```csharp
services.AddSingleton<ISearcher,Searcher>();
```

**性能**

![](https://img1.dotnet9.com/2023/07/0301.png)

## 项目地址

https://github.com/lionsoul2014/ip2region
