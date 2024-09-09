---
title: 基于 C# 开源一个 Windows 屏幕工具箱
slug: open-source-a-windows-screen-toolbox-based-on-csharp
description: Windows屏幕工具，功能包括：屏幕截图、贴图、屏幕取色、截图文字、表格识别（需要申请百度OCR服务）、截图翻译、划词翻译。
date: 2024-03-08 04:47:41
lastmod: 2024-03-08 04:58:17
copyright: Reprinted
banner: false
author: 半栈程序员
originalTitle: 基于 C# 开源一个 Windows 屏幕工具箱
originalLink: https://mp.weixin.qq.com/s/OZcpGBaDddsXFDBajosxEw
draft: false
cover: https://img1.dotnet9.com/2024/03/0205.gif
categories: 
    - .NET
tags: 
    - .NET
    - 工具箱
    - 屏幕截图
    - 贴图
    - 屏幕取色
    - 截图文字
    - 表格识别
    - 截图翻译
    - 划词翻译
---

## **屏幕工具箱**

- 你是不是每次要截图而需要打开微信或者 QQ 截图而感到麻烦，
- 你是不是经常被类似某度文库不能复制文字而感到不爽。
- 你是不是在需要获取屏幕上某个颜色而到处找工具。
- 你是不是想将屏幕操作生成动图图分享给其他人~~~

最近在遇到以上需求时寻找很久还是未能找到合适的方便的工具，于是自己动手写了一个 Windows 屏幕工具，功能包括：屏幕截图、贴图、屏幕取色、截图文字、表格识别（需要申请百度 OCR 服务）、截图翻译、划词翻译。

## **功能预览**

### **主界面**

![](https://img1.dotnet9.com/2024/03/0201.png)

### **开机启动设置**

设置完开机启动后就不用每次手动打开，每次需要截图或文字识别等功能就可以直接使用。

![](https://img1.dotnet9.com/2024/03/0202.gif)

### **快捷键设置**

可通过快捷键设置对截图、贴图、文字识别、表格识别等功能进行快捷调用。

![](https://img1.dotnet9.com/2024/03/0203.gif)

### **截图文字识别**

通过截取屏幕中文字区域，将该区域中文字提取出来，并自动复制到剪切板，直可以直接粘贴到所需要的地方。

![](https://img1.dotnet9.com/2024/03/0204.gif)

### **屏幕取色器**

屏幕取色器提取某个像素点的 RGB、HSB、HSL、HSV、CMYK 等颜色值。

![](https://img1.dotnet9.com/2024/03/0205.gif)

### **GIF 录屏**

将屏幕操作录制成 GIF 动图，包含全屏录制、自定义区域录制、捕获鼠标、保存文件或者复制到剪切板等功能。

![](https://img1.dotnet9.com/2024/03/0206.gif)

项目地址：

[https://github.com/luotengyuan/MyScreenTools](https://github.com/luotengyuan/MyScreenTools)
