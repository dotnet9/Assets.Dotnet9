---
title: 开源.NET 7和Blazor组合开发的跨平台边缘采集网-ThingsGateway
slug: Cross-platform-edge-collection-network-developed-by-combining-open-source-dotnet-7-and-Blazor-ThingsGateway
description: ThingsGateway 基于net6/7+ ，跨平台边缘采集(物联网)网关，支持南北端插件式开发，支持常用Modbus/OPCDA/OPCUA/S7采集插件，MQTT/OPCUAServer等上传插件
date: 2023-05-11 20:48:46
copyright: Contributes
author: Diego
draft: false
cover: https://img1.dotnet9.com/2023/05/cover_02.png
categories: 
    - Blazor
tags: 
    - Blazor
---

> 本文由网友投稿，欢迎更多的朋友来分享。
>
> 作者：Diego
>
> 仓库地址：https://gitee.com/diego2098/ThingsGateway


![](https://img1.dotnet9.com/2023/05/0201.png)

 <table>
    <tr>
        <td><img src="https://img1.dotnet9.com/2023/05/0202.png"/></td>
        <td><img src="https://img1.dotnet9.com/2023/05/0203.png"/></td>
        <td><img src="https://img1.dotnet9.com/2023/05/0204.png"/></td>
    </tr>
    <tr>
        <td><img src="https://img1.dotnet9.com/2023/05/0205.png"/></td>
        <td><img src="https://img1.dotnet9.com/2023/05/0206.png"/></td>
        <td><img src="https://img1.dotnet9.com/2023/05/0207.png"/></td>
    </tr>
        <tr>
        <td><img src="https://img1.dotnet9.com/2023/05/0208.png"/></td>
        <td><img src="https://img1.dotnet9.com/2023/05/0209.png"/></td>
        <td><img src="https://img1.dotnet9.com/2023/05/0210.png"/></td>
    </tr>
 </table>

## 介绍

基于Net6/7+Blazor Server的跨平台边缘采集网关，支持南北端插件式开发

## 功能亮点

- Blazor Server架构，开发部署更简单
- 采集/上传配置完全支持Excel导入导出
- 插件式驱动，方便驱动二次开发
- 时序数据库存储
- 实时/历史报警(Sql转储)，支持布尔/高低限值

## 框架依赖

-  Furion
-  SqlSugar
-  Masa.Blazor
-  TouchSocket
-  ......

## 演示地址

http://120.24.62.140:5000/

默认账户密码：superAdmin 111111

## 采集插件

> 支持分包解析/订阅
- Modbus(Rtu/Tcp/Udp)
- OPCDAClient（支持导入节点）
- OPCUAClient（支持导入节点）
- 西门子S7协议

## 上传插件

> 支持Rpc写入

- Modbus Server
- OPCUA Server (支持历史查询)
- Mqtt Server (支持自定义json)
- Mqtt Client (支持自定义json)
- IotSharp Client (IotSharp网关插件，Rpc待测试)

> 不支持Rpc

- RabbitMQ (支持自定义json)
- Kafka

## nuget

网关项目也提供基础的通讯库Nuget包

- Modbus库，支持ModbusTcp、ModbusRtu、ModbusRtuOverTcp、ModbusUdp、ModbusServer等

```powershell
 dotnet add package ThingsGateway.Foundation.Adapter.Modbus
```

- OPCDA客户端库，支持X64，支持NetCore，支持检测重连

```powershell
 dotnet add package ThingsGateway.Foundation.Adapter.OPCDA
```

- OPCUA客户端库
```powershell
 dotnet add package ThingsGateway.Foundation.Adapter.OPCUA
```

- S7库
``` powershell
 dotnet add package ThingsGateway.Foundation.Adapter.Siemens
```

##  效果图

 ![](https://img1.dotnet9.com/2023/05/0202.png)

 ![](https://img1.dotnet9.com/2023/05/0203.png)

 ![](https://img1.dotnet9.com/2023/05/0204.png)

 ![](https://img1.dotnet9.com/2023/05/0205.png)

 ![](https://img1.dotnet9.com/2023/05/0206.png)

 ![](https://img1.dotnet9.com/2023/05/0207.png)

 ![](https://img1.dotnet9.com/2023/05/0208.png)

 ![](https://img1.dotnet9.com/2023/05/0209.png)

 ![](https://img1.dotnet9.com/2023/05/0210.png)

 ## 例子

 以ModbusTcp采集，Mqtt转发为例

 - **[MdbusTcp设备采集](https://diego2098.gitee.io/thingsgateway/docs/08%E3%80%81Demo/modbusdemo)**
 - **[Mqtt转发](https://diego2098.gitee.io/thingsgateway/docs/08%E3%80%81Demo/mqttclientdemo)**


 ##  文档

 使用前请查看Gitee Pages [文档站点](https://diego2098.gitee.io/thingsgateway/)


## 补充说明

* 使用OPC相关插件时请遵循OPC基金会的授权规则
* 使用OPCDA插件时，需安装OPC核心库，[文件地址](https://gitee.com/diego2098/ThingsGateway/attach_files)

## 开源协议

请仔细阅读授权协议 [Apache License 2.0](https://diego2098.gitee.io/thingsgateway/docs/)

##  联系作者

 * QQ群：605534569
 * 邮箱：2248356998@qq.com