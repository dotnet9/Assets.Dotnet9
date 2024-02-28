---
title: 2022年底C# 解压zip文件遇到的一个Bug
slug: At-the-end-of-2022-one-bug-encountered-by-Csharp-decompressing-zip-files
description: 最近在排查一个上传功能时，客户端上传的是zip文件，到服务器端后使用C# 解压zip文件的代码将上传文件解压后验证是否是允许上传的文件类型，并且要验证乱改文件后缀啊，文件头什么的都要走一遭，结果解压zip文件时就出妖蛾子了。
date: 2022-12-23 09:39:26
copyright: Contributes
author: 江湖人士
originaltitle: 2022年底C# 解压zip文件遇到的一个bug
originallink: https://jhrs.com/2022/46060.html
draft: false
cover: https://img1.dotnet9.com/2022/12/cover_01.png
categories: .NET
tags: .NET
---

> 本文由网友投稿。
>
> 作者：江湖人士
>
> 原文标题：2022年底C# 解压zip文件遇到的一个bug
>
> 原文链接：https://jhrs.com/2022/46060.html


最近在排查一个上传功能时，客户端上传的是zip文件，到服务器端后使用C# 解压zip文件的代码将上传文件解压后验证是否是允许上传的文件类型，并且要验证乱改文件后缀啊，文件头什么的都要走一遭，结果解压zip文件时就出妖蛾子了。

## C# 解压zip文件

先说一下前文（或者上下文），在IIS上部署了一个文件服务站点，用于上传各类文件，流程上是先上传到站点根目录里面随机创建的一个临时目录（这里采用偷懒方案，直接使用guid做为目录名创建），先通过文件验证后再将其通过代码剪切或者复制到正式存档目录，[C# 复制或者移动文件](https://jhrs.com/2022/45307.html)的代码可以参考[江湖人士](https://jhrs.com/)网的这篇文章。昨天快下班时发现上传zip文件时报错，在文件服务根站点创建了很多很多的guid开头的目录，我的妹呀，这下玩犊子了，事出反常必有妖啊，肯定代码出错了。

![](https://img1.dotnet9.com/2022/12/0101.png)

### 有bug的解压代码

这都马上2022年底了，出了这bug后，赶紧搭建个模拟环境跑一下，发现如下原来的代码确实有问题，原始代码如下：

```csharp
/// <summary>
/// 解压文件
/// </summary>
/// <param name="saveDir">保存目录</param>
/// <param name="stream"></param>
public static void UnZipFiles(string saveDir, Stream stream)
{
    using (ZipInputStream s = new ZipInputStream(stream))
    {
        ZipEntry theEntry;
        while ((theEntry = s.GetNextEntry()) != null)
        {
            string directoryName = $"{saveDir}{Path.GetDirectoryName(theEntry.Name)}\\";
            string fileName = Path.GetFileName(theEntry.Name);
            Directory.CreateDirectory(directoryName);
            using (FileStream streamWriter = File.Create(directoryName + fileName))
            {
                byte[] data = new byte[2048];
                while (true)
                {
                    int size = s.Read(data, 0, data.Length);
                    if (size > 0)
                    {
                        streamWriter.Write(data, 0, size);
                    }
                    else
                    {
                        break;
                    }
                }
            }
        }
    }
}
```
       
对了，这里需要说明一下，这是以前的老项目，因此压缩，解压使用了 `ICSharpCode.SharpZipLib.Zip` 这个组件。

当打开源码来看，一眼就发现了问题所在，逻辑不严谨导致，解压文件保存目录直接拼接。

### 如何修复此bug？

知道了问题所在，修复自然简单，调用[Path.Combine](https://learn.microsoft.com/zh-cn/dotnet/api/system.io.path.combine?view=net-7.0)方法即可，解压时再判断一下是目录还是文件即可，最终修复后的代码如下：

```csharp
/// <summary>
/// 解压文件
/// </summary>
/// <param name="saveDir">保存目录</param>
/// <param name="stream"></param>
public static void UnZipFiles(string saveDir, Stream stream)
{
    using (ZipInputStream s = new ZipInputStream(stream))
    {
        ZipEntry theEntry;
        string directoryName, file, fileName;
        while ((theEntry = s.GetNextEntry()) != null)
        {
            directoryName = Path.Combine(saveDir, Path.GetDirectoryName(theEntry.Name));
            fileName = Path.GetFileName(theEntry.Name);
            Directory.CreateDirectory(directoryName);
            file = Path.Combine(directoryName, fileName);

            if (theEntry.IsFile)
            {
                using (FileStream streamWriter = File.Create(file))
                {
                    byte[] data = new byte[2048];
                    while (true)
                    {
                        int size = s.Read(data, 0, data.Length);
                        if (size > 0)
                        {
                            streamWriter.Write(data, 0, size);
                        }
                        else
                        {
                            break;
                        }
                    }
                }
            }
        }
    }
}
```