---
title: ThingsGateway(一)采集ModbusTcp协议设备
slug: ThingsGateway-1-acquisition-ModbusTcp-protocol-device
description: ThingsGateway是国内新生开源项目,归属工业数据采集网关,经过近四个月的洗礼,已经趋于稳定。
date: 2023-07-16 15:55:27
copyright: Contributes
author: Diego
draft: false
cover: https://img1.dotnet9.com/2023/05/cover_02.png
categories: 
    - Blazor
tags: 
    - .NET
---

> 本文由网友投稿，欢迎更多的朋友来分享。
>
> 作者：Diego
>
> 仓库地址：https://gitee.com/diego2098/ThingsGateway
>
>原文链接：https://www.cnblogs.com/ThingsGateway/articles/17557709.html

Gitee源代码仓库：https://gitee.com/diego2098/ThingsGateway

Github源代码仓库：https://github.com/kimdiego2098/ThingsGateway

使用文档：https://diego2098.gitee.io/thingsgateway-docs/

## 一. 前言

<div align="center"> 

[![NuGet(ThingsGateway)](https://img.shields.io/nuget/v/ThingsGateway.Foundation.svg?label=ThingsGateway.Foundation)](https://www.nuget.org/packages/kimdiego/)
[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg)](https://www.apache.org/licenses/LICENSE-2.0.html)
[![star](https://gitee.com/diego2098/ThingsGateway/badge/star.svg)](https://gitee.com/diego2098/ThingsGateway/stargazers) 
[![star](https://gitee.com/diego2098/ThingsGateway/badge/fork.svg)](https://gitee.com/diego2098/ThingsGateway/members) 
![star](https://img.shields.io/badge/QQ群-605534569-blue)

</div>  

**ThingsGateway**是国内新生开源项目,归属工业数据采集网关,经过近四个月的洗礼,已经趋于稳定。

这篇文章将实测**ThingsGateway**采集ModbusTcp协议设备,通过动图演示,方便理解。

## 二. 准备测试环境

1、[ThingsGateway](https://gitee.com/diego2098/ThingsGateway)

2、ModbusSalve

运行ThingsGateway的方法请查看[源文档](https://diego2098.gitee.io/thingsgateway-docs/docs/startguide)。

## 三. 通讯测试

### 3.1. 建立采集设备

![](https://img1.dotnet9.com/2023/07/0402.gif)

建立采集设备，选择ModbusTcp插件，查看设备扩展属性，可以看到ModbusTcp的可配置项。

目前测试我们使用的是本机的502端口，所以默认不修改。

### 3.2 建立变量

![](https://img1.dotnet9.com/2023/07/0403.gif)

建立变量，填写变量名称与变量地址、数据类型。

变量地址为40001，是Modbus协议中的保持寄存器0，详细地址规则请查看源文档。

### 3.3. 重启采集线程

![](https://img1.dotnet9.com/2023/07/0404.gif)

通过运行状态-右上角的浮标，重启全部线程，重启完成后可以看到设备信息。

图中显示设备离线，并提示最后一次错误信息，明显我还没有启动ModbusSlave进行测试。

### 3.4. 启动Modbus服务端

![](https://img1.dotnet9.com/2023/07/0405.gif)

启动ModbusSlave，选择ModbusTcp协议、端口502。

启动后可以看到网关显示设备在线，并倒序显示读写报文。

### 3.5. 查看实时数据

![](https://img1.dotnet9.com/2023/07/0406.gif)

通过运行状态-采集设备-相关变量跳转，或者直接点菜单的实时数据页面，可以看到相关的变量实时数据。

通过ModbusSlave的自增模拟功能，可以看到网关采集的数据变化效果(网关显示页面为1s刷新频率)。

**至此，简单的通讯测试已经结束了。**

## 四. 进阶玩法

### 4.1. 数据转换

试想一下应用场景，当气体仪表通讯值规定是 实际值*100 或者其他的复杂转换，通过网关将完美解决复杂的表达式转换。

![](https://img1.dotnet9.com/2023/07/0407.gif)

编辑变量的读取表达式为 raw/100.0 ，重启线程后可以看到实时值和原始值的区别。

### 4.2. 多个变量分包解析

当一个ModbusTcp有几万个变量时，如果是逐个读取的效率实在是太低，通过网关的分包限制将非常简单得解决问题。

![](https://img1.dotnet9.com/2023/07/0408.gif)


比如现测试的ModbusTcp插件，只需要修改最大打包长度(默认100)，或者直接默认。

可以看到实际通讯报文，在读取时只需要分送一次请求。

### 4.3. 采集冗余

![](https://img1.dotnet9.com/2023/07/0409.gif)

冗余的概念大伙应该熟悉，网关的采集冗余也是如此。

配置冗余设备后，当采集设备出现离线3次以上的情况，将切换至备用设备。

## 五. 关于.NET

下图截自.NET官网：https://dotnet.microsoft.com/zh-cn/，核心思想：.NET免费、开源、快速和跨平台、新式和高效：

![](https://img1.dotnet9.com/2023/07/0401.png)

### 5.1. .NET免费、开源

.NET 是一个免费的开放源代码项目，它在 GitHub 上开发和维护，而 GitHub 是数百万希望一起创建出色内容的开发人员的家园。

### 5.2. .NET快速和跨平台

根据 TechEmpower，.NET 的执行速度快于任何其他常用框架。可以在多个平台(包括 Windows、Linux 和 macOS)上编写、运行和生成。

### 5.3. .NET新式和高效

.NET 可帮助你构建适用于 Web、移动、桌面、云等的应用。.NET 具有强大的支持生态系统和强大的工具，对于开发人员而言是最高效的平台。