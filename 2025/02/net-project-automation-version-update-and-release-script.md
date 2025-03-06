---
title: .NET 项目自动化秘籍：一键更新版本与发布脚本全解析
slug: net-project-automation-version-update-and-release-script
description: 文章详细介绍了如何利用 PowerShell 脚本和批处理文件在 .NET Avalonia UI 项目中实现自动更新程序版本和一键发布。首先，文章解释了 PowerShell 执行策略的设置和修改，以确保脚本能够正常执行。接着，介绍了在 Visual Studio 预生成事件中添加脚本来自动更新版本号的方法，以及如何使用批处理文件在多个平台发布应用程序。最后，提供了一个 PowerShell 脚本示例，该脚本可以根据 Git 标签自动更新程序的版本信息。这些方法能够提高 .NET项目的开发效率和发布流程的便捷性。
date: 2025-02-09 15:29:39
lastmod: 2025-02-21 22:30:23
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2025/02/cover_01.png
categories:
  - Avalonia
tags:
  - C#
  - Avalonia
  - 发布
  - publish
---

在.NET 开发的旅程中，高效的版本管理和便捷的发布流程是提升开发效率和项目质量的关键环节。今天，我们就来深入探讨一下如何利用脚本实现.NET Avalonia UI 项目（当然，这些方法也适用于其他.NET 项目）的自动更新程序版本以及一键发布，让你的开发过程更加流畅和高效。

#### PowerShell 脚本执行权限检查

在运行 PowerShell 脚本之前，了解并正确设置执行策略是至关重要的。执行策略决定了 PowerShell 是否允许运行脚本以及运行何种类型的脚本。你可以使用以下简单的命令来查看当前的执行策略：

```powershell
Get-ExecutionPolicy
```

不同的执行策略值有着不同的含义，对脚本的运行有着不同程度的限制：

- **Restricted**：这是默认策略，它就像是一个严格的门卫，不允许运行任何脚本，你只能在 PowerShell 控制台中逐行输入命令。这在一定程度上保证了系统的安全性，但对于需要批量执行脚本的开发者来说，无疑是一种束缚。
- **AllSigned**：只允许运行由受信任的发布者签名的脚本。这就好比只允许持有特定通行证的人进入，确保了脚本来源的可靠性，但也增加了脚本使用的门槛，因为并非所有脚本都有签名。
- **RemoteSigned**：本地创建的脚本可以直接运行，而从互联网下载的脚本必须由受信任的发布者签名才能运行。这是一个在安全性和便捷性之间取得平衡的设置，既允许本地开发的灵活性，又对网络下载的脚本进行了安全把关。
- **Unrestricted**：允许运行所有脚本，不过从互联网下载的脚本在运行前会提示确认。这给予了开发者极大的自由度，但也伴随着一定的安全风险，因为未经严格检查的脚本可能携带恶意代码。
- **Bypass**：不阻止任何脚本的运行，也不会有安全提示。这是一个非常开放的设置，一般仅在特定的、安全可控的环境中使用，否则可能会让系统暴露在安全威胁之下。

默认情况下，执行策略是 Restricted，这可能会导致回写文件失败，提示不允许写入流。例如，当你尝试运行一个需要修改文件内容的脚本时，就会遇到权限问题。

#### 修改执行策略

为了能够顺利运行脚本，我们需要根据自己的需求选择合适的执行策略。如果希望在保证一定安全性的同时，能够方便地运行本地脚本和经过一定安全检查的网络下载脚本，将执行策略设置为 RemoteSigned 是一个不错的选择。

但需要注意的是，修改执行策略可能需要管理员权限，所以请务必以管理员身份运行 PowerShell，然后使用以下命令进行修改：

```powershell
Set-ExecutionPolicy RemoteSigned
```

这样，本地创建的脚本可以直接运行，而从互联网下载的脚本则需要经过签名验证，有效降低了安全风险。

#### 验证修改结果

修改执行策略后，为了确保设置生效，你可以再次使用 Get-ExecutionPolicy 命令来验证是否修改成功。当看到输出结果为 RemoteSigned 时，就意味着你已经成功修改了执行策略，之后应该就可以正常运行.ps1 脚本了。

#### VS 发布

在 Visual Studio 中，我们可以通过在预生成事件中添加脚本来实现项目版本的自动更新。具体操作如下：在项目属性中找到 “生成事件” 选项卡，在 “预生成事件命令行” 中添加以下命令：

```powershell
powershell -ExecutionPolicy Bypass -File "UpdateAssemblyVersion.ps1" -AssemblyInfoFile "GlobalAssembly.cs" -Configuration "$(ConfigurationName)" -Platform "$(PlatformName)"
```

这里的参数有着明确的含义：

- **ConfigurationName**：表示活动解决方案配置，常见的如 Debug（调试模式）和 Release（发布模式）。在不同的配置下，我们可能希望生成不同版本号的程序，以区分开发和正式发布的版本。
- **PlatformName**：表示活动解决方案平台，例如 AnyCPU（适用于任何 CPU 架构）、x86（32 位架构）、x64（64 位架构）等。不同的平台可能需要不同的版本标识，以满足兼容性和性能需求。

#### 批处理文件发布

有时候，我们可能希望在不打开 Visual Studio 的情况下发布程序，并且能够方便地扩展到发布 Windows、Linux、macOS 等多个平台。这时候，批处理文件就派上用场了。以下是一个示例批处理文件：

```batch
@echo off
setlocal enabledelayedexpansion

rem 遍历 GlobalAssemblies 目录下的文件执行 PowerShell 脚本
for %%f in (GlobalAssemblies\*) do (
    powershell -ExecutionPolicy Bypass -File "UpdateAssemblyVersion.ps1" -AssemblyInfoFile "%%f" -Configuration "Release" -Platform "x64"
    rem 检查 PowerShell 脚本执行是否成功
    if !errorlevel! neq 0 (
        echo Failed to update assembly version for file %%f!
        endlocal
        exit /b !errorlevel!
    )
)

rem 设置发布路径
set PUBLISH_PATH=publish\win-x64

rem 定义项目信息数组，格式为 项目名称,项目路径（只写项目目录）,相对发布目录名
set "projects=码坊工具箱,src\CodeWF.Toolbox.Desktop,codewf Avalonia发布测试,tests\AvaloniaAotDemo,AvaloniaAotDemo"

rem 遍历项目数组
for %%a in ("%projects: =","%") do (
    set "current_project=%%~a"
    for /f "tokens=1,2,3 delims=," %%b in ("!current_project!") do (
        set "projectName=%%b"
        set "projectPath=%%c"
        set "relativePublishDir=%%d"

        rem 拼接 .pubxml 文件的默认路径
        set "pubxmlPath=!projectPath!\Properties\PublishProfiles\FolderProfile-win_x64.pubxml"
        rem 拼接当前项目的发布路径
        set "CURRENT_PUBLISH_DIR=!PUBLISH_PATH!\!relativePublishDir!"

        echo "projectName after assignment: !projectName!"
        echo "projectPath after assignment: !projectPath!"
        echo "relativePublishDir after assignment: !relativePublishDir!"
        echo "pubxmlPath after assignment: !pubxmlPath!"
        echo "current publish dir after assignment: !CURRENT_PUBLISH_DIR!"


        echo Publishing !projectName! for win-64...
        rem 清空发布目录
        if exist "!CURRENT_PUBLISH_DIR!" (
            rd /s /q "!CURRENT_PUBLISH_DIR!"
        )
        rem 创建发布目录
        mkdir /p "!CURRENT_PUBLISH_DIR!" 2>nul
        rem 执行 dotnet publish 命令进行 AOT 发布，并指定目标框架
        dotnet publish "!projectPath!" /p:PublishProfile="!pubxmlPath!" -f net9.0-windows -o "!CURRENT_PUBLISH_DIR!"
        rem 检查 dotnet publish 命令的退出代码
        if !errorlevel! neq 0 (
            echo Publish of !projectName! failed!
            endlocal
            exit /b !errorlevel!
        )
        rem 检查发布目录是否存在
        if exist "!CURRENT_PUBLISH_DIR!" (
            rem 删除调试符号文件
            del "!CURRENT_PUBLISH_DIR!\*.pdb"
        )
        echo Publish of !projectName! succeeded!
        echo Published files of !projectName! are located at: "!CURRENT_PUBLISH_DIR!"
    )
)

endlocal
```

这个批处理文件首先遍历 GlobalAssemblies 目录下的文件，执行 PowerShell 脚本来更新程序版本。然后，它定义了项目信息数组，包括项目名称、项目路径和相对发布目录名。接着，通过循环遍历项目数组，依次对每个项目进行发布操作。在发布过程中，先清空发布目录，再创建新的发布目录，然后执行 dotnet publish 命令进行 AOT 发布，并指定目标框架为 net9.0-windows。最后，检查发布是否成功，如果成功则删除调试符号文件，并输出成功信息和发布路径。

#### PowerShell 脚本实现自动版本更新

下面这个 PowerShell 脚本用于根据 Git 标签（Tag）自动更新程序版本：

```powershell
param(
	[Parameter(Mandatory=$true)]
	[string]$AssemblyInfoFile,

	[Parameter(Mandatory=$true)]
	[string]$Configuration,

	[Parameter(Mandatory=$true)]
	[string]$Platform
)

# 检查文件是否存在
if (-not (Test-Path $AssemblyInfoFile)) {
    throw "错误：文件 $AssemblyInfoFile 不存在"
}

Write-Output "AssemblyInfoFile: $AssemblyInfoFile"
Write-Output "Configuration: $Configuration"
Write-Output "Platform: $Platform"



#获取当前日期时间，并生成格式化的时间戳
$currentDateTime =Get-Date
#获取当前分支，签出标签创建时间[基准时间]
$strBranchCreateTime =git log -1 --format=%ai $latest_tag
$dateBranchCreateTime=[DateTime]::Parse($strBranchCreateTime)
#时间差的总分数
$timeDifMinutes =[math]::Round(($currentDateTime-$dateBranchcreateTime).TotalMinutes)
#版本号格式处理成x.x.65535.65535
$yy=[math]::Floor($timeDifMinutes/65535)
$thirdInfo=$timeDifMinutes-$yy*65535
#读取文件内容
$content = Get-Content -Path $AssemblyInfoFile -Encoding Unicode
#使用正则表达式获职 AssemblyProduct 的值
$productPattern='^\[assembly: AssemblyProduct\("([^"]+)"\)\]'
$product = ""
#查找包含 AssemblyProduct 的行，并匹配正则表达式
foreach($line in $content){
	if($line -match $productPattern){
		$product= $matches[1]
		break #找到匹配后退出循环
	}
}
$items=$product -split '_'
$product=$items[0]
#当前最近标签、checkout hash值
$currentCommitHash=git describe --always --tag --long
$items=$currentCommitHash.Split('-')
$hashInfo=$items[$items.Length-1]
$firstFileVersion="0.0"
$secondFileVersion="100"
if($items.Length -eq 3)
{
	$firstFileVersion=$items[0].Replace("v","");
	$firstFileItems=$firstFileVersion.split('.');
	$firstFileversion=$firstFileItems[0]+"."+$firstFileItems[1]
	$secondFileVersion=$items[1]+"$yy".PadLeft(2,'0')
}

$platformInfo = ""
if ($Configuration -eq "Debug") {
	$platformInfo = "D"
} elseif ($Configuration -eq "Release") {
	$platformInfo = "R"
} else {
	$platformInfo = "A"
}
switch ($Platform) {
	"x86" {
		$platformInfo += "-86"
	}
	"x64" {
		$platformInfo += "-64"
	}
	"ARM" {
		$platformInfo += "-ARM"
	}
	"AnyCPU" {
		$platformInfo += "-AnyCPU"
	}
	default {
		$platformInfo += "-Unknow"
	}
}

#定义新的 AssemblyProduct和 AssemblyFileversion
$strDayInfo= $currentDateTime.Tostring("yyyyMMddHHmm")
$newAssemblyProduct = "$product"+"_"+"$platformInfo"+"_"+"$hashInfo"+"_"+"$strDayInfo"
if($secondFileVersion -eq "000")
{
	$secondFileVersion="0"
}
$newAssemblyFileVersion = "$firstFileVersion"+"."+"$secondFileVersion"+"."+"$thirdInfo"

# 更新 AssemblyProduct
$content = $content -replace '^\[assembly: AssemblyProduct\(".*"\)\]', "[assembly: AssemblyProduct(`"$newAssemblyProduct`")]"

# 更新AssemblyVersion
$content = $content -replace '^\[assembly: AssemblyVersion\(".*"\)\]', "[assembly: AssemblyVersion(`"$newAssemblyFileVersion`")]"

# 更新AssemblyFileVersion
$content = $content -replace '^\[assembly: AssemblyFileVersion\(".*"\)\]', "[assembly: AssemblyFileVersion(`"$newAssemblyFileVersion`")]"

# 将更新后的内容写入 AssemblyInfo.cs 文件
Set-Content -Path $AssemblyInfoFile -Value $content -Encoding Unicode
```

这个脚本首先接收三个参数：AssemblyInfoFile（程序集信息文件路径）、Configuration（配置名称）和 Platform（平台名称）。然后，它检查文件是否存在，如果不存在则抛出错误。接着，通过获取当前日期时间、Git 标签创建时间等信息，计算出版本号的各个部分。根据配置和平台信息，生成新的 AssemblyProduct 和 AssemblyFileVersion。最后，使用正则表达式替换文件中的旧版本信息，将更新后的内容写入 AssemblyInfo.cs 文件，实现了程序版本的自动更新。

通过以上这些脚本和方法，我们可以极大地提高.NET 项目的开发效率，实现版本管理和发布流程的自动化。希望这些内容能对你的开发工作有所帮助，让你在.NET 开发的道路上更加得心应手。

以上示例脚本可在开源项目找到：[dotnet9/CodeWF.Toolbox: CodeWF Toolbox 使用 Avalonia 开发的跨平台工具箱 Cross platform toolbox developed using Avalonia](https://github.com/dotnet9/CodeWF.Toolbox)
