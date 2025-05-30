---
title: (11/30)大家一起学Blazor：Arbitrary属性
slug: 11-of-30-let-us-learn-blazor-together-arbitrary-property
description: 目前`MyButton`有3个`[Parameter]`，如果再增加的话，又要再定义新的`[Parameter]`，为了避免不断更新这个Component，我们来用Blazor提供的`@attribute`。
date: 2021-12-15 22:53:17
copyright: Reprinted
author: StrayaWorker
originalTitle: (11/30)大家一起学Blazor：Arbitrary属性
originalLink: https://ithelp.ithome.com.tw/articles/10262490
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_05.png
albums:
    - 一起学Blazor系列
categories: 
    - Blazor
tags: 
    - Blazor
    - ASP.NET Core
    - 学Blazor
---

目前`MyButton`有 3 个`[Parameter]`，如果再增加的话，又要再定义新的`[Parameter]`，为了避免不断更新这个 Component，我们来用 Blazor 提供的`@attribute`。

首先把原本的`<button>`改为`<input>`，在`MyButton.razor`定义一个带有`[Parameter]`的`InputAttributes`，类型为`Dictionary<string, object>`，先给初始值，如果外部没有传入这个参数的话就会套用初始值。

![](https://img1.dotnet9.com/2021/12/1701.png)

接着在`PostBase.razor.cs`也定义一个同名变量，不过里面的`value`跟`class`有稍微改动。

![](https://img1.dotnet9.com/2021/12/1702.png)

最后`Post.razor`调用的`MyButton`只传入`InputAttributes`，这样就完成了。

![](https://img1.dotnet9.com/2021/12/1703.png)

不过好像哪里怪怪的，因为只用了一个变量`InputAttributes`，导致原本的 Reset 按钮跟 Submit 变得一样了，但如果再定义一个为 Reset 产生的变量，又跟原本一样麻烦了。

![](https://img1.dotnet9.com/2021/12/1704.png)

这时候就要用到`[Parameter]`的参数`CaptureUnmatchedValues`了，我们将原本的`[Parameter]`改成`[Parameter(CaptureUnmatchedValues = true)]`或是`[Parameter(true)]`也行，告诉 Blazor 这个变量会捕捉任何不符合`InputAttributes`中定义的值。

![](https://img1.dotnet9.com/2021/12/1705.png)

接着把`PostBase.razor.cs`的变量`InputAttributes`删除，因为不能再这样定义了，我们等一下来看看如果保留的话会有什么结果。

![](https://img1.dotnet9.com/2021/12/1706.png)

最后`PostBase.razor`随意给`<MyButton>`我们想要的 html 属性，就可以看到变回 Submit 跟 Reset 按钮了，要注意的是因为变成我们自由定义，所以没有了强类型的约束。

![](https://img1.dotnet9.com/2021/12/1707.png)

![](https://img1.dotnet9.com/2021/12/1708.png)

这时候有人可能会发现，两个`<MyButton>`有个相同 html 属性`type`，既然重复，可以定义在`PostBase.razor.cs`的`InputAttributes`吗？

![](https://img1.dotnet9.com/2021/12/1709.png)

可惜错误信息告诉我们：如果用了`CaptureUnmatchedValues`就不能明确定义`InputAttributes`了，所以使用的人须特别注意这点。

![](https://img1.dotnet9.com/2021/12/1710.png)

还有一点是 html 属性的读取顺序是由右到左，若有重复的 html 属性，右边的会覆盖左边的，我们在`MyButton.razor`的`@attribute`右边加上`value="MyButton"`，打开网页可以看到两颗按钮都变成 Mybutton 了，所以若要给初始值的话，最好是放在`@attribute`左边，以免覆盖传进来的值。

![](https://img1.dotnet9.com/2021/12/1711.png)

**引用：**

1. [Blazor Attribute Splatting](https://www.pragimtech.com/blog/blazor/blazor-attribute-splatting/)
2. [Ref: Arbitrary attributes and parameters in Blazor](https://www.pragimtech.com/blog/blazor/blazor-arbitrary-attributes/)

**注：本文代码通过 .NET 6 + Visual Studio 2022 重构，可点击原文链接与重构后代码比较学习，谢谢阅读，支持原作者**
