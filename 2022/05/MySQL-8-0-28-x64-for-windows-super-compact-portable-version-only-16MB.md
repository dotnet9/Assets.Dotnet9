---
title: mysql 8.0.28 x64 for Windows超级精简便携版，仅16MB
slug: MySQL-8-0-28-x64-for-windows-super-compact-portable-version-only-16MB
description: MySQL是一个关系型数据库管理系统，由瑞典MySQL AB 公司开发，目前属于 Oracle 旗下产品。
date: 2022-05-06 07:31:24
copyright: Reprinted
author: 懒得勤快
originaltitle: mysql 8.0.28 x64 for Windows超级精简便携版，仅16MB
originallink: https://ldqk.xyz/1567?kw=mysql&t=uptl3b9wq874
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_23.png
categories: 数据库
tags: MySQL
---

![](https://img1.dotnet9.com/2022/05/cover_23.png)

MySQL是一个关系型数据库管理系统，由瑞典MySQL AB 公司开发，目前属于 Oracle 旗下产品。MySQL 是最流行的关系型数据库管理系统之一，在 WEB 应用方面，MySQL是最好的 RDBMS应用软件。MySQL是一种关系数据库管理系统，关系数据库将数据保存在不同的表中，而不是将所有数据放在一个大仓库内，这样就增加了速度并提高了灵活性。MySQL所使用的 SQL 语言是用于访问数据库的最常用标准化语言。MySQL 软件采用了双授权政策，分为社区版和商业版，由于其体积小、速度快、总体拥有成本低，尤其是开放源码这一特点，一般中小型网站的开发都选择 MySQL 作为网站数据库。由于其社区版的性能卓越，搭配 PHP 和 Apache 可组成良好的开发环境。

## 1. mysql8.0功能介绍

1．限制

如果 WHERE 子句的查询条件里有不等号（WHERE coloum !=），MySQL 将无法使用索引。类似地，如果 WHERE 子句的查询条件里使用了函数（WHERE DAY（column）=），MySQL 也将无法使用索引。在 JOIN 操作中（需要从多个数据表提取数据时），MySQL 只有在主键和外键的数据类型相同时才能使用索引。如果 WHERE 子句的查询条件里使用比较操作符 LIKE 和 REGEXP，MySQL 只有在搜索模板的第一个字符不是通配符的情况下才能使用索引。比如说，如果查询条件是 LIKE 'abc%‘，MySQL 将使用索引；如果查询条件是 LIKE '%abc’，MySQL 将不使用索引。在 ORDER BY 操作中，MySQL 只有在排序条件不是一个查询条件表达式的情况下才使用索引。（虽然如此，在涉及多个数据表查询里，即使有索引可用，那些索引在加快 ORDER BY 方面也没什么作用）。如果某个数据列里包含许多重复的值，就算为它建立了索引也不会有很好的效果。比如说，如果某个数据列里包含的净是些诸如 “0/1” 或 “Y/N” 等值，就没有必要为它创建一个索引。从理论上讲，完全可以为数据表里的每个字段分别建一个索引，但 MySQL 把同一个数据表里的索引总数限制为16个。

2．InnoDB 数据表的索引

与 InnoDB数据表相比，在 InnoDB 数据表上，索引对 InnoDB 数据表的重要性要大得多。在 InnoDB 数据表上，索引不仅会在搜索数据记录时发挥作用，还是数据行级锁定机制的基础。“数据行级锁定”的意思是指在事务操作的执行过程中锁定正在被处理的个别记录，不让其他用户进行访问。这种锁定将影响到（但不限于）SELECT、LOCKINSHAREMODE、SELECT、FORUPDATE 命令以及 INSERT、UPDATE 和 DELETE 命令。出于效率方面的考虑，InnoDB 数据表的数据行级锁定实际发生在它们的索引上，而不是数据表自身上。显然，数据行级锁定机制只有在有关的数据表有一个合适的索引可供锁定的时候才能发挥效力。

## 2. 精简说明

精简掉了除mysql主服务之外的其余多余服务和扩展组件，以及pdb文件，从而将体积缩小到了200多MB，压缩后只有13，适合个人学习以及没有其他额外需求的用户使用。

## 3. 使用说明

下载解压到指定目录后，得到如下文件：

![](https://img1.dotnet9.com/2022/05/2301.png)

分别有三个脚本：

- startConsole.bat：直接启动mysql服务器
- install.bat：将mysql安装成Windows服务
- uninstall.bat：卸载mysql服务

用户名：root，密码空 

## 4. 下载地址

[https://ldqk.lanzouy.com/iooMWz1w5pi](https://ldqk.lanzouy.com/iooMWz1w5pi)

更多内容请点击下面链接访问懒得勤快官网，特别是文中链接失效时，哈哈。