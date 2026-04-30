---
title: WPF 英雄联盟
slug: wpf-lol
description: 您可以了解如何正确实施 WPF 项目
date: 2022-06-09 23:11:27
lastmod: 2022-06-09 23:11:27
author: 驚鏵 WPF开发者
originalTitle: WPF 英雄联盟
originalLink: https://mp.weixin.qq.com/s/vgha1ZRGYU40zR6g7v_1uA
copyright: Reprinted
draft: false
cover: https://img1.dotnet9.com/2022/06/1001.png
categories:
  - WPF
tags:
  - .NET
  - WPF
---

> WPF 英雄联盟
>
> 作者：Devncore 组织 来自 韩国，首尔
>
> 原文链接：https://github.com/devncore/leagueoflegends

- 感谢分享者[晨晞 gg](https://www.cnblogs.com/chenxigg/)；
- 框架使用`.NET6`；
- `C# 10.0`;
- `Visual Studio 2022`;

![](https://img1.dotnet9.com/2022/06/1001.png)

- 您可以了解如何正确实施 `WPF` 项目。
- 描述了如何在不依赖商业组件的情况下，直接实现英雄联盟等顶级设计领域的表达。
- 您可以通过自己实现 `MVVM` 模式来详细学习和理解 `WPF`。
- 更多效果可以通过[GitHub](https://github.com/devncore/leagueoflegends)下载代码,使用`Visual Studio 2022`打开解决方案`Leagueoflegends.sln`将`Leagueoflegends`项目设为启动项；

![](https://img1.dotnet9.com/2022/06/1002.png)

### 预览原文

### WPF League of Legends

WPF 기반으로 만든 **리그오브레전드**입니다.

![](https://img1.dotnet9.com/2022/06/1003.png)

## 컨텐츠

- [이 오픈소스의 특징](#이-오픈소스의-특징)
- [개발 정보](#개발-정보)
- [프로젝트 구조](#프로젝트-구조)
- [데이터베이스](#데이터베이스)
- [스크린샷](#스크린샷)

## 이 오픈소스의 특징

- WPF 프로젝트를 올바르게 구현하는 방법을 학습할 수 있습니다.
- **리그오브레전드**와 같은 최상위 디자인 영역의 표현을 상용 컴포넌트에 의지하지 않고 직접 구현하는 방법에 대해 설명합니다.
- MVVM 패턴을 직접 구현하여 WPF에 대해 자세하게 이해하고 학습할 수 있습니다.

## 개발 정보

- .NET 6.0
- C# 10.0
- [Visual Studio 2022](https://visualstudio.microsoft.com/ko/vs/preview/vs2022/)

## NuGet Package (1.0.9)

- [DevNcore.WPF](https://github.com/devncore/devncore)
- [DevNcore.UI.Foundation](https://github.com/devncore/devncore)
- [DevNcore.UI.Design](https://github.com/devncore/devncore)
- [DevNcore.UI.Design.Converter](https://github.com/devncore/devncore)
- [DevNcore.UI.Design.Geometry](https://github.com/devncore/devncore)
- [DevNcore.LayoutSupport.Leagueoflegends](https://github.com/devncore/devncore)

## 프로젝트 구조

- 📁 AppData
- 📁 Based
- 📁 Implement
- 📁 Material
- 📁 Presentation
- **Leagueoflegends**

## 데이터베이스

**WPF League of Legends**는 클래식 **RDB** 대신 **YAML**을 데이터베이스로 사용하고 있습니다.

> YAML은 JSON과 함께 널리 사용되는 데이터 양식입니다.  
> 이 기술에 대한 내용은 **[Guide to Yaml](https://github.com/devncore/guide-to-yaml)** 에서 더 자세히 학습할 수 있습니다.

## 스크린샷

### `Home`

![](https://img1.dotnet9.com/2022/06/1004.png)

### `TFT`

![](https://img1.dotnet9.com/2022/06/1005.png)

### `Clash`

![](https://img1.dotnet9.com/2022/06/1006.png)

![](https://img1.dotnet9.com/2022/06/1007.png)

### `Setting`

![](https://img1.dotnet9.com/2022/06/1008.png)

### `Profile`

![](https://img1.dotnet9.com/2022/06/1009.png)

### `Collection`

![](https://img1.dotnet9.com/2022/06/1010.png)

![](https://img1.dotnet9.com/2022/06/1011.png)

![](https://img1.dotnet9.com/2022/06/1012.png)

![](https://img1.dotnet9.com/2022/06/1013.png)

### `Loot`

![](https://img1.dotnet9.com/2022/06/1014.png)

### `My Shop`

![](https://img1.dotnet9.com/2022/06/1015.png)

### `Store`

![](https://img1.dotnet9.com/2022/06/1016.png)

![](https://img1.dotnet9.com/2022/06/1017.png)

![](https://img1.dotnet9.com/2022/06/1018.png)

### `Game`

![](https://img1.dotnet9.com/2022/06/1019.png)

![](https://img1.dotnet9.com/2022/06/1020.png)
