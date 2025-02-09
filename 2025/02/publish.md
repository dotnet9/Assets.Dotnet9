---
title: Avalonia发布
slug: publish-avalonia
description: 记录发布
date: 2025-02-09 15:29:39
lastmod: 2025-02-09 15:52:21
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2025/01/cover_01.png
categories: 
    - Avalonia
tags: 
    - C#
    - Avalonia
    - 发布
    - publish
---

## PowerShell脚本执行权限检查

在运行 PowerShell 时，你可以使用以下命令来查看当前的执行策略：

```powershell
Get-ExecutionPolicy
```

常见的执行策略值及其含义如下：
- Restricted：默认策略，不允许运行任何脚本，只能在 PowerShell 控制台中逐行输入命令。
- AllSigned：只允许运行由受信任的发布者签名的脚本。
- RemoteSigned：本地创建的脚本可以直接运行，但从互联网下载的脚本必须由受信任的发布者签名才能运行。
- Unrestricted：允许运行所有脚本，不过从互联网下载的脚本在运行前会提示确认。
- Bypass：不阻止任何脚本的运行，也不会有安全提示。

默认是Restricted，回写文件会失败：不允许写入流

**修改执行策略**

你可以根据自己的需求选择合适的执行策略，并使用以下命令进行修改。需要注意的是，修改执行策略可能需要管理员权限，因此你要以管理员身份运行 PowerShell。

将执行策略设置为 RemoteSigned，这是一个比较安全且常用的设置，允许本地脚本直接运行，同时对从互联网下载的脚本进行一定的安全检查。

```powershell
Set-ExecutionPolicy RemoteSigned
```

**验证修改结果**

修改执行策略后，你可以再次使用 Get-ExecutionPolicy 命令来验证是否修改成功。之后，你应该就可以正常运行 .ps1 脚本了。