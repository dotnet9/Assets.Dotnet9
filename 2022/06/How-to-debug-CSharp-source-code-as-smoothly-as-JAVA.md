---
title: 像JAVA一样流畅调试C#源代码？
slug: How-to-debug-CSharp-source-code-as-smoothly-as-JAVA
description: 有没有一种可能, C#也能像JAVA那样非常顺畅的调试源代码呢？
date: 2022-06-29 21:03:38
copyright: Reprinted
author: 长空X
originaltitle: 像JAVA一样流畅调试C#源代码？
originallink: https://www.cnblogs.com/gtxck/articles/16423094.html
draft: False
cover: https://img1.dotnet9.com/2022/06/cover_21.png
categories: .NET
tags: Java
---

## 起因

最近在研究`ServiceScope`的内一些内在运行逻辑,发现相关资料非常少，只有讲`IOC`相关的文章有说`Core时代`的官方`依赖注入`怎么使用。。遂决定还是要去看源代码。这部分源代码在`Microsoft.Extensions.DependencyInjection`库中，源代码位置在[src/libraries](https://github.com/dotnet/runtime/tree/release/6.0/src/libraries/Microsoft.Extensions.DependencyInjection)下。阅读了一点，发现内部解析服务的时候会来回倒腾,那看代码的方式去梳理就非常难受了。。

有没有一种可能, C#也能像JAVA那样非常顺畅的调试源代码呢？

## 效果

还真有！  话不说多，看图:

![](https://img1.dotnet9.com/2022/06/2101.png)

![](https://img1.dotnet9.com/2022/06/2102.png)

速度非常快，像调试本地代码一样.. 比反编译出来的流畅度不知道高到那里去了！

不知道官方的项目用了什么`黑魔法`，这里能直接拉到源代码（图里的外部源），而自己开发的项目做不到这一点。

## 具体步骤

这块其实官方有说明，但漏了几个关键点导致我卡了非常久，下面会进行详细说明:

- PS1： 以Windows VS为主，其它平台应该类似

- PS2: 我主要是查看DI的构建逻辑，这块在不同版本差异不大, 所以我直接获取了6.0

### 1.打开官方仓库

[官方仓库](https://github.com/dotnet/runtime)

然后你拉取你想看的分支代码到本地,我主要是看

![](https://img1.dotnet9.com/2022/06/2103.png)

### 2.找到他们的构建说明

![](https://img1.dotnet9.com/2022/06/2104.png)

### 3.安装对应平台的基础环境

![](https://img1.dotnet9.com/2022/06/2105.png)

Windows VS平台是这样安装的：

![](https://img1.dotnet9.com/2022/06/2106.png)

![](https://img1.dotnet9.com/2022/06/2107.png)

然后点击查看详细信息，弹出的提示(`无法安装XXXXX`)可以忽略, 然后点修改即可。

这一步，官方的说法是你只需要安装更高版本的SDK即可，不用一一匹配。通常情况下开发的电脑上都会安装.NET Framework和.NET的几个SDK，一般都有。我自己是安装 .NET Framework 4.0目标包+4.7.2、目标包+NET 6.0的SDK。

### [重点]4. 还原对应库

资源浏览器定位到`runtime的根目录`，记住这个`build.cmd`

![](https://img1.dotnet9.com/2022/06/2108.png)

右键打开命令行或pwd，像这样执行：

![](https://img1.dotnet9.com/2022/06/2109.png)

脚本会下载一个`ps1`文件然后自动执行，我们等待即可，他会自动还原我们需要的库，并且把依赖的基础包也一并还原好。

官方的代码结构中已经做好了nuget配置和输出目录, 我们已经不需要额外配置了,下一步进行编译。

### 5.生成对应库的dll文件

打开对应库的代码文件:

![](https://img1.dotnet9.com/2022/06/2110.png)

右键打开命令行或pwd，像这样执行：

![](https://img1.dotnet9.com/2022/06/2111.png)

等待编译结束去这个目录下找东西

![](https://img1.dotnet9.com/2022/06/2112.png)

每个库都会生成到`artifacts`下面，然后不同架构对应一个文件夹, 此时你就可以在你的测试项目中`直接引用这个dll`了, 愉快的调试吧。

### [可选]6.生成依赖库文件

我这里是想调试`Microsoft.Extensions.DependencyInjection`，在Nuget上就能看到他还依赖一个抽象定义包`Microsoft.Extensions.DependencyInjection.Abstractions`，为了不在调试中卡壳，我把这个包一并如法炮制。

## 其它

1. 在查阅资料时发现其实也可以用VS直接编译，但需要配置些东西，我没看明白就用这个办法了，我也不需要编译所有的。
2. `VS Code`也可以，但我主要用VS就略过这部分了
3. `build.cmd`脚本不加参数似乎是编译所有包, 我不需要就跳过这个了


## 参考资料

1. [官方构建文档](https://github.com/dotnet/runtime/blob/main/docs/workflow/requirements/windows-requirements.md)
2. [关键构建流程](https://github.com/dotnet/runtime/blob/main/docs/workflow/README.md)
