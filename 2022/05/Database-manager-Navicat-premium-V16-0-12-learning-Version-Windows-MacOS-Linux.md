---
title: 数据库管理利器——Navicat Premium v16.0.12学习版(Windows+MacOS+Linux)
slug: Database-manager-Navicat-premium-V16-0-12-learning-Version-Windows-MacOS-Linux
description: Navicat Premium 是一套数据库管理工具，让你以单一程序同時连接到 MySQL、MariaDB、SQL Server、SQLite、Oracle 和 PostgreSQL 数据库。
date: 2022-05-06 07:12:11
copyright: Reprinted
author: 懒得勤快
originalTitle: 数据库管理利器——Navicat Premium v16.0.12学习版(Windows+MacOS+Linux)
originalLink: https://ldqk.xyz/37?t=uptjw5h2j0n4
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_22.png
categories: 
    - 分享
tags: 
    - 软件分享
---

![](https://img1.dotnet9.com/2022/05/cover_22.png)

Navicat Premium 是一套数据库管理工具，让你以单一程序同時连接到 MySQL、MariaDB、SQL Server、SQLite、Oracle 和 PostgreSQL 数据库。 此外，它与 Drizzle、OurDelta 和 Percona Server 兼容，并支持 Amazon RDS、Amazon Aurora、Amazon Redshift、SQL Azure、Oracle Cloud 和 Google Cloud 等云数据库。

结合了其他 Navicat 成员的功能，Navicat Premium 支持大部份在现今数据库管理系统中使用的功能，包括存储过程、事件、触发器、函数、视图等。

Navicat Premium 能使你快速地在各种数据库系统间传输数据，或传输到一份指定 SQL 格式和编码的纯文本文件。计划不同数据库的批处理作业并在指定的时间运行。其他功能包括导入向导、导出向导、查询创建工具、报表创建工具、数据同步、备份、工作计划及更多。

Navicat 的功能足以符合专业开发人员的所有需求，但是对数据库服务器的新手来说又相当容易学习。

![](https://img1.dotnet9.com/2022/05/2201.png)

## 新功能

相比上一个版本，Navicat 16 带来了许多 UI/UX 改进。我们致力于提供专业的 UX 设计，以提高可用性和可访问性。因此，你能够以前所未有的速度完成复杂的工作。

![](https://img1.dotnet9.com/2022/05/2202.png)

![](https://img1.dotnet9.com/2022/05/2203.png)

![](https://img1.dotnet9.com/2022/05/2204.png)

![](https://img1.dotnet9.com/2022/05/2205.png)

![](https://img1.dotnet9.com/2022/05/2206.png)

## 下载地址

官方 Windows 安装包：http://download.navicat.com.cn/download/navicat160_premium_cs_x64.exe

`可使用无限试用脚本，将以下代码粘贴到记事本另存为bat文件执行即可：`

```shell
@echo off

echo Delete HKEY_CURRENT_USER\Software\PremiumSoft\NavicatPremium\Registration[version and language]
for /f %%i in ('"REG QUERY "HKEY_CURRENT_USER\Software\PremiumSoft\NavicatPremium" /s | findstr /L Registration"') do (
    reg delete %%i /va /f
)
echo.

echo Delete Info folder under HKEY_CURRENT_USER\Software\Classes\CLSID
for /f %%i in ('"REG QUERY "HKEY_CURRENT_USER\Software\Classes\CLSID" /s | findstr /E Info"') do (
    reg delete %%i /va /f
)
echo.

echo Finish

pause
```

更多内容请点击下面链接访问懒得勤快官网，特别是文中链接失效时，哈哈。
