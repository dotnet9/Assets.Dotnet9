---
title: 分享PC微信多开的方法
slug: Share-the-method-of-opening-more-PC-WeChat
description: 多开微信
date: 2022-03-18 07:32:16
copyright: Default
originaltitle: 分享PC微信多开的方法
draft: False
cover: https://img1.dotnet9.com/2022/03/cover_21.png
categories: 技巧分享
tags: 微信多开
---

很简单，创建一个bat文件，比如`openwechat.bat`，然后复制以下内容到该文件：

```shell
@echo off
start "" "C:\Program Files (x86)\Tencent\WeChat\WeChat.exe"
start "" "C:\Program Files (x86)\Tencent\WeChat\WeChat.exe"
exit
```

记得把`你电脑上的微信exe路径`复制到上面的内容中去，然后双击就可以启动2个微信了，当然`想运行几个你就写几行`，相信你看的懂。