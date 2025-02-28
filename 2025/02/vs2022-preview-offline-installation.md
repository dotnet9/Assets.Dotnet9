---
title: VS 2022预览版离线安装包制作指南
slug: vs2022-preview-offline-installation
description: 离线开发环境搭建是最麻烦的，本文详细介绍如何制作VS 2022预览版的离线安装包，包括完整的下载、安装步骤和注意事项
date: 2025-02-26 13:05:24
lastmod: 2025-02-27 09:18:12
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2025/02/cover_07.png
categories:
  - 开发工具
  - Visual Studio
tags:
  - Visual Studio 2022
  - 离线安装
  - 开发环境
---

## 准备工作

1. 下载 Visual Studio 2022 预览版引导程序
   - 访问[Visual Studio 预览版下载页面](https://visualstudio.microsoft.com/zh-hans/vs/preview/)
   - 选择并下载 Enterprise/Professional/Community 版本的引导程序

2. 准备存储空间
   - 建议预留至少 200GB 的硬盘空间
   - 选择一个网络访问良好的环境

## 制作离线安装包

### 1. 使用命令行下载

打开命令提示符（管理员），执行以下命令：

```shell
VisualStudioSetup.exe --layout D:\2022 --noweb --add Microsoft.VisualStudio.Workload.NetWeb Microsoft.VisualStudio.Workload.NetDesktop --includeRecommended --lang Zh-cn en-US
```

参数说明：
- `--add`：用于指定[工作负载或组件 ID](https://learn.microsoft.com/zh-cn/visualstudio/install/workload-and-component-ids?view=vs-2022)。
  如果使用 `--add`，则仅下载由 `--add` 指定的工作负载和组件。 如果不使用 `--add`，将下载所有工作负载和组件。
- `--includeRecommended`，用于添加针对指定工作负载 ID 的所有推荐组件。
- `--includeOptional`，用于添加针对指定工作负载 ID 的所有可选组件。
- `--config` 使用 `*.vsconfig` 文件来指定 [工作负载、组件或扩展](https://learn.microsoft.com/zh-cn/visualstudio/install/create-a-network-installation-of-visual-studio?view=vs-2022#use-a-configuration-file-to-initialize-the-contents-of-a-layout)，这些工作负载、组件或扩展应包含在布局中或由布局引用。 请确保指定配置文件的完整路径。
- `--lang`：用于指定[语言区域设置](https://learn.microsoft.com/zh-cn/visualstudio/install/use-command-line-parameters-to-install-visual-studio?view=vs-2022#list-of-language-locales)。

常用工作负载代号

- .NET 桌面开发：`Microsoft.VisualStudio.Workload.NetDesktop`
- ASP.NET Web 开发：`Microsoft.VisualStudio.Workload.NetWeb`
- .NET 跨平台开发：`Microsoft.VisualStudio.Workload.NetCrossPlat`
- Azure 开发：`Microsoft.VisualStudio.Workload.Azure`
- 数据存储和处理：`Microsoft.VisualStudio.Workload.Data`

上面是参考官网文档的部分参数示例，但实际上我们可以使用更简单的命令：

```shell
VisualStudioSetup.exe --layout D:\2022
```

这个简化命令的优势在于：
1. 会下载所有可用组件，确保不会遗漏需要的功能
2. 包含所有语言包，适用于多语言开发团队
3. 避免因参数配置不当导致的组件缺失问题

虽然这种方式会占用更多存储空间（约80GB，压缩后约66GB），但在网络条件允许的情况下，是最省心的方案。

**注意：若要创建整个产品的布局，并使用最新和最佳的安装程序，请运行**

```shell
VisualStudioSetup.exe --layout D:\2022 --useLatestInstaller
```

期间可能会遇到部分包下载失败：

![](https://img1.dotnet9.com/2025/02/0701.png)

上面的Unity3d因为签名问题就下载失败了，但我不做相关开发，所以可以忽略，最后制作的安装包也能正常安装，如果需要对应的包可再研究。

### 2. 离线安装

在目标机器上，直接双击目录下的"VisualStudioSetup.exe"进行安装即可。

## 注意事项

1. 确保下载期间网络稳定
2. 下载完成后，压缩后传递到目标机器，比如FTP
3. 如需更新布局，使用相同命令即可增量更新
4. 建议定期更新离线包以获取最新的安全补丁

## 常见问题解决

1. 下载中断
   - 重新运行相同命令，将继续未完成的下载

2. 安装失败
   - 检查证书是否正确安装
   - 确认目标机器满足系统要求
   - 查看日志文件排查具体原因

3. 组件缺失
   - 检查工作负载是否正确指定
   - 确认 `--includeRecommended` 和 `--includeOptional` 参数的使用

## 总结

通过以上步骤，我们可以制作一个完整的 Visual Studio 2022 预览版离线安装包。这对于网络受限的环境特别有用，可以确保开发团队使用统一的开发环境。记得定期更新离线包以获取最新的功能和安全更新。

**参考：**

- [离线 Visual Studio安装包制作教程](https://learn.microsoft.com/zh-cn/visualstudio/install/create-a-network-installation-of-visual-studio?view=vs-2022)
- [Visual Studio 2022 预览版引导程序](https://visualstudio.microsoft.com/zh-hans/vs/preview/)
