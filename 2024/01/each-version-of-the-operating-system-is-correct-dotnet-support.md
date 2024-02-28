---
title: 各版本操作系统对.NET支持情况
slug: each-version-of-the-operating-system-is-correct-dotnet-support
description: 借助虚拟机和测试机，检测各版本操作系统对.NET的支持情况。安装操作系统后，实测安装相应运行时并能够运行星尘代理为通过。
date: 2024-01-13 15:51:26
lastmod: 2024-01-13 16:13:28
banner: true
copyright: Reprinted
author: 大石头
originaltitle: 各版本操作系统对.NET支持情况（1124更新）
originallink: https://newlifex.com/tech/os_net
draft: false
cover: https://img1.dotnet9.com/2024/01/cover_02.png
categories: .NET
tags: 技术更新
---

借助虚拟机和测试机，检测各版本操作系统对.NET的支持情况。

安装操作系统后，实测安装相应运行时并能够运行星尘代理为通过。

测试平台：VMware Workstation

镜像来源：[MSDN, 我告诉你 - 做一个安静的工具站 (itellyou.cn)](https://msdn.itellyou.cn/)

参考：

- [.NET Framework 版本和依赖关系](https://learn.microsoft.com/zh-cn/dotnet/framework/migration-guide/versions-and-dependencies)
- [.NET Framework 系统要求](https://learn.microsoft.com/zh-cn/dotnet/framework/get-started/system-requirements)

# WinXP系列

| 系统                      | .NET2.0 SP2    | .NET3.5 SP1   | .NET4.0          | .NET4.5 | NativeAOT |
| ------------------------- | -------------- | ------------- | ---------------- | ------- | --------- |
| WindowXP Professional     | 失败。要求SP2  | 失败。要求SP2 | 失败。系统不支持 | 失败    | 失败      |
| WindowXP Professional SP2 | 支持。kb893803 | 支持          | 支持             | 失败    | 失败      |
| WindowXP Professional SP3 | 支持           | 支持          | 支持             | 失败    | 失败      |
| WindowXP Home             | 失败。要求SP2  | 失败。要求SP2 | 失败。系统不支持 | 失败    | 失败      |
| WindowXP Home SP3         | 支持           | 支持          | 支持             | 失败    | 失败      |
| Windows 2000 Professional |                |               |                  |         |           |
| Windows 2003              |                |               |                  |         |           |
| Windows 2003 R2           | 支持           | 支持          | 支持             | 失败    | 失败      |

win2003r2需要先安装net3.5sp1，才能支持安装net2sp2，也不能提前安装net4.0

# Win7/Vista系列

| 操作系统              | 自带    | NET4 | .NET4.5 | .NET4.8           | NET6 | NET7 | NET8 | NativeAOT        |
| --------------------- | ------- | ---- | ------- | ----------------- | ---- | ---- | ---- | ---------------- |
| Win7 Enterprise x86   | .NET3.5 | 支持 |         |                   |      | 失败 |      |                  |
| Win7 Enterprise       | .NET3.5 | 支持 | 支持    | 失败              |      | 失败 |      | 失败             |
| Win7 Enterprise SP1   | .NET3.5 | 支持 | 支持    | 支持。需KB3063858 | 支持 | 支持 |      | 支持。需vc++2019 |
| Win7 Professional     | .NET3.5 | 支持 | 支持    | 失败              |      | 失败 |      | 失败             |
| Win7 Professional SP1 | .NET3.5 | 支持 | 支持    | 支持。需KB3063858 | 支持 | 支持 | 支持 | 支持。需vc++2019 |
| Win7 Ultimate         | .NET3.5 | 支持 | 支持    | 失败              |      | 失败 | 失败 | 失败             |
| Win7 Ultimate SP1     | .NET3.5 | 支持 | 支持    | 支持。需KB3063858 | 支持 | 支持 | 支持 | 支持。需vc++2019 |
| Win7 Ultimate SP1 x86 |         |      |         |                   |      |      | 支持 |                  |
| Vista Business        | .NET2.0 | 支持 | 支持    | 失败              |      | 失败 |      | 失败             |
| Vista Enterprise SP2  | .NET3.0 | 支持 | 支持    | 失败              |      | 失败 |      | 失败             |
| Win2008 SP2           | .NET2.0 | 支持 | 支持    | 失败              |      | 失败 |      | 失败             |
| Win2008 R2 SP1        | .NET4.0 | 支持 | 支持    | 支持。证书链      |      | 支持 |      | 支持。需vc++2019 |

win7打上sp1以后，可以安装vc++2019，然后就能跑AOT应用了 。

win7能够安装net7，但是占用内存很大，空白应用启动起码占500M内存，官方直接说net7不支持win7。

# Win8系列

| 操作系统        | 自带      | .NET4.8           | .NET7.0 | NativeAOT                |
| --------------- | --------- | ----------------- | ------- | ------------------------ |
| Windows 8       | .NET4.5   | 失败。不支持      | 支持    | 支持                     |
| Windows 8.1     | .NET4.5.1 | 支持。需KB2919355 | 支持    | 失败。缺vc++2019但装不上 |
| Windows 2012    | .NET4.5   | 支持              | 支持    | 支持。需vc++2019         |
| Windows 2012 R2 | .NET4.5.1 | 支持。需KB2919355 | 支持    | 失败。缺vc++2019但装不上 |

# Win10/Win11系列

| 操作系统             | 自带       | .NET4.8 | NET7 | NET8 | AOT8 |
| -------------------- | ---------- | ------- | ---- | ---- | ---- |
| Windows 10 LTSC 2019 | .NET 4.7.2 | 支持    | 支持 | 支持 | 支持 |
| Windows 10 22H2      | .NET 4.8   | 支持    | 支持 | 支持 | 支持 |
| Windows 11 22H2      | .NET 4.8   | 支持    | 支持 | 支持 | 支持 |
| Windows 2016         | .NET 4.6.1 | 支持    | 支持 |      | 支持 |
| Windows 2016 VL      | .NET 4.6.2 | 支持    | 支持 |      | 支持 |
| Windows 2019         | .NET 4.7.2 |         |      |      | 支持 |
| Windows 2019 UP2020  |            |         |      |      | 支持 |
| Windows 2022         |            |         |      |      |      |

# Linux系列

| 操作系统               | Mono      | NET3.1 | NET6 | NET7 | NET8 | NativeAOT | 备注                |
| ---------------------- | --------- | ------ | ---- | ---- | ---- | --------- | ------------------- |
| Deepin 20              | Mono 5.18 |        | 支持 | 支持 |      | 支持      | 容易                |
| Ubuntu 16              |           |        |      |      | 支持 |           |                     |
| Ubuntu 18              |           |        |      |      | 支持 |           |                     |
| Ubuntu 20              | Mono 6.8  |        | 支持 | 支持 |      | 支持      | 较容易              |
| Debian 11              | Mono 6.8  |        | 支持 | 支持 |      | 支持      |                     |
| CentOS 7.6             |           |        |      |      | 支持 |           | NET8需替换libstdC++ |
| CentOS 8               |           |        |      |      |      |           |                     |
| Kali 2022.3            | Mono 6.12 | 支持   | 支持 | 支持 |      | 支持      | 自带.NETCore3.1     |
| Fedora 37              |           |        | 支持 | 支持 |      | 支持      |                     |
| UOS 20 Home            | Mono 5.18 |        | 支持 | 支持 |      | 支持      |                     |
| UOS 20 Pro Arm64       |           |        |      | 支持 |      |           | HUAWEI Kunpeng 920  |
| UOS 20 Pro Mips64      |           | 支持   | 失败 | 失败 |      | 失败      | Loongson-3          |
| openKylin              | Mono 6.12 |        | 支持 | 支持 |      | 支持      | 容易                |
| NeoKylin7              |           |        | 支持 | 支持 |      | 支持      |                     |
| Keylin Desktop V10 SP1 | Mono 6.12 |        | 支持 | 支持 |      | 支持      |                     |
| Keylin Server V10 SP1  | Mono 6.12 |        | 支持 | 支持 |      | 支持      |                     |
| Kylin V10 SP1          |           |        |      | 支持 |      |           | Phytium,FT-2000+/64 |
| SmartOS A4             |           |        | 支持 | 支持 | 支持 |           |                     |

感谢 [@_well](https://www.yuque.com/_well) 在UOS上的支持

# .NET Framework 版本历史

| 版本                                                         | 发布日期       | 终止支持      |
| ------------------------------------------------------------ | -------------- | ------------- |
| [.NET Framework 4.8.1](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net481) | 2022年8月9日   |               |
| [.NET Framework 4.8](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net48) | 2019年4月18日  |               |
| [.NET Framework 4.7.2](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net472) | 2018年4月30日  |               |
| [.NET Framework 4.7.1](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net471) | 2017年10月17日 |               |
| [.NET Framework 4.7](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net47) | 2017年4月5日   |               |
| [.NET Framework 4.6.2](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net462) | 2016年8月2日   |               |
| [.NET Framework 3.5 SP1](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net35-sp1) | 2008年11月18日 | 2029年1月9日  |
| [.NET Framework 4.6.1](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net461) | 2015年11月30日 | 2022年4月26日 |
| [.NET Framework 4.6](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net46) | 2015年7月20日  | 2022年4月26日 |
| [.NET Framework 4.5.2](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net452) | 2014年5月5日   | 2022年4月26日 |
| [.NET Framework 4.5.1](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net451) | 2013年10月17日 | 2016年1月12日 |
| [.NET Framework 4.5](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net45) | 2012年8月15日  | 2016年1月12日 |
| [.NET Framework 4.0](https://dotnet.microsoft.com/zh-cn/download/dotnet-framework/net40) | 2010年4月12日  | 2016年1月12日 |



# Windows自带及最高支持

| Windows版本                    | 自带.NET Framework 版本          | 支持最高的 .NET Framework 版本 |
| ------------------------------ | -------------------------------- | ------------------------------ |
| Windows NT 4.0 SP6a、2000      |                                  | .NET Framework 1.1 SP1         |
| Windows 98, 98SE, Me, 2000 SP3 |                                  | .NET Framework 2.0             |
| Windows 2000 SP4               |                                  | .NET Framework 2.0 SP2         |
| Windows XP SP1                 | .NET Framework 1.0 SP2           | .NET Framework 1.0 SP2         |
| Windows XP SP2                 | .NET Framework 1.1 SP1           | .NET Framework 3.5 SP1         |
| **Windows XP SP3**             | **.NET Framework 1.1 SP1**       | **.NET Framework 4.0**         |
| Windows Vista                  | .NET Framework 3.0               | .NET Framework 3.5 SP1         |
| Windows Vista SP1              | .NET Framework 3.0 SP1           | .NET Framework 4.0             |
| Windows Vista SP2              | .NET Framework 3.0 SP2           | .NET Framework 4.6             |
| Windows 7                      | .NET Framework 3.5.1 SP1         | .NET Framework 4.5             |
| **Windows 7 SP1**              | **.NET Framework 3.5.1 SP1**     | **Latest**                     |
| Windows 8                      | .NET Framework 3.5.1 SP1 + 4.5   | .NET Framework 4.6.2           |
| Windows 8.1                    | .NET Framework 3.5.1 SP1 + 4.5.1 | .NET Framework 4.5.2           |
| Windows 8.1 Update             | .NET Framework 3.5.1 SP1 + 4.5   | Latest                         |
| **Windows 10 （1507）**        | **.NET Framework 4.6**           | **Latest**                     |
| Windows 10 （1511）            | .NET Framework 4.6.1             | Latest                         |
| Windows 10 （1607）            | .NET Framework 4.6.2             | Latest                         |
| Windows 10 （1703）            | .NET Framework 4.7               | Latest                         |
| Windows 10 （1709）            | .NET Framework 4.7.1             | Latest                         |
| Windows 10 （1803 ~ 1809）     | .NET Framework 4.7.2             | Latest                         |
| Windows 10 （1903 ~ v20H2）    | .NET Framework 4.8               | Latest                         |
| Windows 11                     | .NET Framework 4.8               | Latest                         |

- 作者：大石头
- 发布：2023-11-24 08:11:07
- 原文链接：[各版本操作系统对.NET支持情况（1124更新） (newlifex.com)](https://newlifex.com/tech/os_net)
