---
title: Avalonia使用XML文件实现国际化
slug: avalonia-using-xml-files-for-internationalization
description: Avalonia UI使用XML文件实现本地化和国际化，用户可灵活的进行用户侧修改、扩展其他支持语言
date: 2024-12-05 21:47:17
lastmod: 2024-12-05 22:39:23
copyright: Original
draft: true
cover: https://img1.dotnet9.com/2024/12/cover_02.png
categories: 
    - .NET
tags: 
    - C#
    - Avalonia UI
    - XML
    - 本地化
    - 国际化
---

1. 为什么使用XML实现Avalonia UI国际化
解决Resx只能开发侧维护，将XML语言文件和可执行程序一起输出，方便用户侧扩展更多语言、已有语言文件修改等
2. 怎么使用？
不同模块XML文件名不能相同，程序编译输出时相同XML文件会替换，导致语言丢失
3. 语言管理
多模块XML文件合并，合并前注意备份
合并原理
XML语言管理
