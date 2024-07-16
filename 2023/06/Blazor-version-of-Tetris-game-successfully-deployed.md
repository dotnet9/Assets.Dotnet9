---
title: Blazor版俄罗斯方块游戏部署成功
slug: Blazor-version-of-Tetris-game-successfully-deployed
description: 上线了Blazor版俄罗斯方块游戏，并且把在线工具和在线游戏组件提取到Razor共享库，可以被Dotnet9网站和Dotnet工具箱网站复用。
date: 2023-06-27 20:58:14
lastmod: 2023-06-27 23:40:59
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2023/06/cover_14.png
categories: .NET
tags: Dotnetools,WASM,Blazor WebAssembly,Client
---

大家好，我是沙漠尽头的狼。

抄了国外大佬的一个俄罗斯方块游戏，也将在线工具和在线游戏组件提取到Razor共享库，可以被 [Dotnet9](https://dotnet9.com) 网站和 [Dotnet工具箱](https://dotnetools.com) 网站复用，这篇分享游戏的搬运及Razor共享库的迁移过程，和这几天开发、部署遇到的一些问题与解决方案记录分享下。

行文目录：
1. 添加俄罗斯方块游戏
2. 组件复用
3. 遇到的问题及解决方案
4. 总结

## 1. 添加俄罗斯方块游戏

### 1.1. 游戏参考:BlazorGames

**BlazorGames项目信息**
- Github：https://github.com/exceptionnotfound/BlazorGames
- 在线访问地址：https://blazorgames.net/

仓库截图如下：

![](https://img1.dotnet9.com/2023/06/1401.png)

网站截图：

![](https://img1.dotnet9.com/2023/06/1402.png)

该网站有系列文章教你怎么用Blazor开发7、8个在线小游戏，课程算比较详细，如上图，游戏有单人纸牌游戏（Solitaire）、俄罗斯方块（Tetris）、扫雷（Minesweeper）等，其中俄罗斯方块的系列文章截图如下：

课程列表地址：https://blazorgames.net/tetris

![](https://img1.dotnet9.com/2023/06/1403.png)

![](https://img1.dotnet9.com/2023/06/1404.png)

### 1.2. 添加到自己的项目

首先请先Clone仓库：https://github.com/exceptionnotfound/BlazorGames

作者代码组织结构很清晰，先理解代码结构：

![](https://img1.dotnet9.com/2023/06/1405.png)

- 1.2.1. `/Pages`下的razor文件

为各个游戏页面，比如`Tetris.razor`，这个文件我们可以直接复制到自己的项目，去掉页面下方的文章链接即可。

- 1.2.2. `/Pages/Partials`下的razor文件

为各个游戏的子组件，如`/Pages/Partials/TetrisGridCell.razor`为俄罗斯方块背景的单元格组件。

- 1.2.3. `/Models`为各个游戏使用的Model类

如`/Models/Tetris/`下有俄罗斯方块的背景表格（Grid.cs）、单元格(Cell.cs)、方块（Block.cs）等定义类。

- 1.2.4. `wwwroot`目录为项目资源目录

使用到的Js库、Css样式、图片、声音文件等都放在这里，通过调试程序、阅读代码，知道如果要在自己的项目正常运行俄罗斯游戏需要这些文件:

应用样式（`css/app.cs`)、游戏部分样式（`css/games.css`)、俄罗斯方块图片（`images/tetrix`）、游戏音乐播放和游戏分数的Cookie读写Js库（`js/utilities.js`）、游戏背景音乐文件（`sounds/tetris-theme.ogg`）等。

以上文件在熟悉后，就可以一边拷贝到自己的项目一边调试了，下面是前面提到的文件部分截图

俄罗斯方块背景的单元格组件：

![](https://img1.dotnet9.com/2023/06/1406.png)

俄罗斯方块的Model类等定义：

![](https://img1.dotnet9.com/2023/06/1407.png)

资源文件截图：

![](https://img1.dotnet9.com/2023/06/1408.png)

## 2. 组件复用

原先Dotnet9网站和Dotnet工具箱网站的在线工具和在线游戏代码是复制了两份，分别维护的，但其中90%代码一样，这是无法容忍的代码冗余。

站长考虑将原先的Dotnet工具箱仓库删掉，代码合并到Dotnet9仓库，将共享的组件提取到Razor共享库内，现改造后的共享库目录结构：

3个主工程：1是Razor共享库，2是Dotnet9网站主工程，3是Dotnet工具箱主工程，2和3都将1添加为项目引用。

![](https://img1.dotnet9.com/2023/06/1401.jpg)

Razor共享库代码组织结构，目前已有的在线工具和在线游戏组件：

![](https://img1.dotnet9.com/2023/06/1409.png)

组件代码在前面几篇文章都贴过，这里略过，但游戏页面的路由这里提一下：Dotnet9网站和Dotnet工具箱的网页布局是不同的，相同的是里面的内容，所以每个工具和游戏在两个工程里都添加了对应的页面路由，比如下面的俄罗斯方块页面在两个工程中的位置：

Dotnet9中的俄罗斯方块页面：

![](https://img1.dotnet9.com/2023/06/1410.png)

Dotnet工具箱的俄罗斯方块页面：

![](https://img1.dotnet9.com/2023/06/1411.png)

两个页面内容几乎完全相同，不同的可能就是页面标题了，相信还可以再优化。

优化不好几百字说清楚，有兴趣的看 [Dotnet9源码](https://github.com/dotnet9/Dotnet9) 吧。

## 3. 遇到的问题及解决方案

开发Dotnet9网站和Dotnet工具箱过程中，包括部署有遇到一些问题，通过查阅资料、问技术群的一些大佬几乎都得到了解决，这里列出一些大家也可能遇到的问题，分享总结下。

### 3.1. Server访问链接过多

上篇站长对Server访问链接过多做了截图，觉得是Server的问题，技术群群友立马做了反驳，是站长nginx配置有问题，没有对Socket有效配置才导致的频繁连接，原文截图如下：

![](https://img1.dotnet9.com/2023/06/1304.png)

解决Nginx参考配置如下：

![](https://img1.dotnet9.com/2023/06/1412.png)

主要关注的节点配置是`proxy_http_version 1.1`，但站长原来是配置过的，昨晚只把`proxy_set_header Connection keep-alive`改为了`proxy_set_header Connection "upgrade"`，请群友验证，目前上面截图的问题貌似解决了，大家也可以打开Dotnet9网站验证下。

### 3.2. Wasm直接打开子页面404

通过Dotnet工具箱网站首页，再访问其他子页面是正常的，然而打开浏览器地址栏，直接粘贴子页面访问，网站会响应404，解决方案也是修改nginx配置：

```json
location / {
    try_files $uri $uri/ @router;
    index  index.html index.htm;
}
location @router {
        rewrite ^.*$ /index.html last;
}
```

主要是其中的`try_files`，至于什么意思请百度。

### 3.3. 项目正常编译，界面显示黑块

本来昨天站长已经发布了Dotnet工具箱关于俄罗斯方块的功能，但游戏的背景界面（黑色背景）老是显示不出来，搞了半天是组件内的组件没有正常加载，即没有将子组件命名空间加上`@using Dotnetools.Share.Components.Partials`。

原始代码如下：

![](https://img1.dotnet9.com/2023/06/1413.png)

问题是通过F12调试网页源码发现的，发现子组件对应的html代码并没有编译为html原生代码，还是组件代码，被直接编译为字符串了，即显示如下：

![](https://img1.dotnet9.com/2023/06/1414.png)

加上命名空间引用后，源码显示正常了，黑色背景也显示出来了：

![](https://img1.dotnet9.com/2023/06/1415.png)

这个问题属于不细心，共享库提取后，没有查看html中razor组件的引用是否正常，这个问题VS是不会给出异常提示的。。。

## 4. 总结

今天分享到这，有什么工具需求请在下方issue链接中留言，十分感谢您的阅读。

- 网站地址：Blazor Server版本网站=》https://dotonet9.com/， Blazor Wasm版本网站=》https://dotnetools.com/
- 网站源码：https://github.com/dotnet9/Dotnet9，Issue=》https://github.com/dotnet9/Dotnet9/issues
- .NET版本： [.NET 8.0.0-preview.5.23280.8](https://dotnet.microsoft.com/zh-cn/download/dotnet/8.0)
- 微信技术群：添加站长微信（codewf），一定要备注【入群】2个字
- QQ技术群：771992300