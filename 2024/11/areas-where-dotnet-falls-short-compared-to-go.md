---
title: 相对于Go，哪些领域是.NET做不到或做不好的?
slug: areas-where-dotnet-falls-short-compared-to-go
description: 看到这个问题的时候，我瞬间有些恍惚，有哪些地方Go能做到？Net会不做到，C#不行呢？
date: 2024-11-09 12:16:45
lastmod: 2024-11-09 12:23:09
copyright: Reprinted
banner: false
author: 明月三千
originalTitle: 相对于Go，哪些领域是.NET做不到或做不好的?
originalLink: https://mp.weixin.qq.com/s/PyXSFBphQtNWRA5_TJJIMQ
draft: false
cover: https://img1.dotnet9.com/2024/11/cover_04.png
categories: 
    - .NET
    - 更多语言
tags: 
    - .NET
    - GO
---

![图片](https://img1.dotnet9.com/2024/11/cover_04.png)

看到这个问题的时候，我瞬间有些恍惚，有哪些地方Go能做到？Net会不做到，C#不行呢？

那我们就掰开了揉碎了，从Go可能胜出的地方，来挨个分析一下：

## 1、交叉编译

在Windows平台上面编译Linux和Mac平台的程序，Go可以，C#应该也可以，通过publish机制一样办得到！

## 2、面向对象

这个应该讨论的是“Go能否达到Net那样的水平？”

虽然Go的interface和struct可以做到“实现和声明”分离，但是C#的interface一样可以，不过没有Go分离的那么彻底，这应该是C#的一个“弱点”。

但同时，C#严格的Interface/Implement机制，也保证了大型项目的安全性，不会出现有些Go程序里面interface{}满天飞的情况！

另外一点，个人觉得C#的Class机制更加符合我的编程思维。很多老一点的程序员，其实都遵循相同的编程轨迹，c到C++，然后Java，可谓一直是Class护体。

写了这么多年的Class，即便是用go，那也是class的灵魂。

不怕各位笑话，我一般都是定义一个struct，然后就开始定义方法，像这样：

```csharp
type Person {   
	Name string   
	Age int 
}  

//下面开始进入C++形态  
func (p Person) CalSome(){

}
func (p *Person) IncSome(){
}  

//开始调用  
p := Person{    
	Name:"ttt",    
	age:45    
}
p.IncSome()
```

仔细想来，这和C#的差别到底有多大，个人觉得，区别不大！

从程序的“美感”来讲，其实C#更美！

## 3、反编译

如果说以前，确实Go在Windows平台上面，编译为一个Exe文件以后，想反编译出Go的源代码，那几乎是不可能的。

C#这边非常容易。如果你知道这是一段Net代码，想反编译的话有很多工具可以使用，包括ILDASM、dotPeek、ILSPY。

当然Java这边也有jd等反编译工具。

现在Net提供了AOT选项之后，其反编译功能应该有很大的进步。AOT编译处理的代码都是本机代码，再想反编译就和Go一个难度了。

分久必合，合久必分，语言期间其实也是各种功能互相“借鉴”。

## 4、Docker影像大小

这个应该是Go胜出的一个领域。

使用Alpine作为Base Image，前几天我做了一个镜像，docker images显示其大小为13M。

如果使用“scratch”镜像作为Base，相信会更小。

而Net的AspNetCore则显然大很多，30M+。

如果说需要很多个Docker Image运行在k8s里面，无疑Go镜像的占用会低很多。

## 5、Father

最后，那就只好比一比老爸了，毕竟富二代出生在罗马，而普通人的人生目标就是罗马！

Go的father是Google。

Net的Father是微软！

一个是有点旧的“新时代霸主”，一个是有点“旧貌换新颜”的Old Meoney！

相比较而言，可能Google要强势一些。

从这点出发，Go略胜，Net略败！

当然，Father的影响也很大，Net从诞生之初就不太受人待见，收到Java的打压。

这一点，好像在我们熊猫国还特别突出。

现在熊猫国的大公司，腾讯已经在把主力语言从C/C++全面转向Go语言；字节跳动则是Go默认是编程语言第一选择，没有特殊需求，一般都是使用Go来开发，Rust尚且排在Go后面；七牛的许志伟则一直就是Go的狂热拥护者，甚至开发了一门Go+语言，相当于Go的改进版本。

目前看来，除了阿里坚守Java阵营，Go在中国大公司里面已经非常流行。相比较而言，Net则生意惨淡，在工业控制、MES、物联网、HIS等领域艰难求生，薪水也不容乐观。

在熊猫国，从人气这一方面，Go无疑完胜Net。

## 总结

从语法、功能角度来讲，Go能做到的，Net也能做到，甚至更好。

从体积来讲，Go可以更轻量级，毕竟Net已经是多年的积累，积重难返！

从人气来讲，特别是互联网领域，Go的使用率完胜Net；则工厂等非互联网领域，Net则更加流行。

一个新贵蔺相如，一个老廉颇，他们能将相和吗？

我是明月，

一个互联网说书人！