---
title: .NET 最好用的开源 FTP 客户端库
slug: The-Best-Open-Source-FTP-Client-Library-for-dotNET
description: FluentFTP 是一个适用于 .NET 和 .NET Standard 的 FTP 和 FTPS 客户端。
date: 2023-07-17 19:59:34
copyright: Reprint
author: 工具箱
originaltitle: .NET 最好用的开源 FTP 客户端库
originallink: https://mp.weixin.qq.com/s/ZwV-hgGlgFlkGY-Yy8oCLA
draft: false
cover: https://img1.dotnet9.com/2023/07/0501.png
categories: .NET相关
---

FluentFTP 是一个适用于 .NET 和 .NET Standard 的 FTP 和 FTPS 客户端。

并且针对速度进行了优化，没有外部依赖， 完全用 C# 编写。

![](https://img1.dotnet9.com/2023/07/0501.png)

## 功能特性

完全支持 FTP、FXP、FTPS、带 TLS 1.3 的 FTPS、带客户端证书的 FTPS和FTPS 代理。

全面支持 30 多种 FTP Server 类型。

支持各种文件和目录列表（Unix、Windows/IIS、Azure、Pure-FTPd、ProFTPD、Vax、VMS、OpenVMS、Tandem、HP NonStop Guardian、IBM z/OS 和 OS/400、Windows CE、Serv- U等）。

支持递归目录列出和进行目录删除。

通过进度跟踪可以轻松从服务器上传和下载文件。

创建、追加、读取、写入、重命名、移动和删除文件和文件夹。

异步支持，所有操作都可以使用 async await。

## C# 使用示例

```csharp
// 通过用户名密码创建连接
var client = new AsyncFtpClient("123.123.123.123", "david", "pass123");

// 连接到服务器，并设置自动重连
await client.AutoConnect();

// 列出所有的文件
foreach (FtpListItem item in await client.GetListing("/htdocs")) {

    // 如果是文件类型
    if (item.Type == FtpObjectType.File) {

        // 获取文件大小
        long size = await client.GetFileSize(item.FullName);

        // 计算文件 hash
        FtpHash hash = await client.GetChecksum(item.FullName);
    }

    // 获取文件或文件夹的修改时间
    DateTime time = await client.GetModifiedTime(item.FullName);
}

// 上传一个文件
await client.UploadFile(@"C:\MyVideo.mp4", "/htdocs/MyVideo.mp4");

// 移动文件
await client.MoveFile("/htdocs/MyVideo.mp4", "/htdocs/MyVideo_2.mp4");

// 下载文件
await client.DownloadFile(@"C:\MyVideo_2.mp4", "/htdocs/MyVideo_2.mp4");
 
// 删除文件
await client.DeleteFile("/htdocs/MyVideo_2.mp4"); 

// 关闭连接，结束
await client.Disconnect();
```

## 项目地址

https://github.com/robinrodricks/FluentFTP