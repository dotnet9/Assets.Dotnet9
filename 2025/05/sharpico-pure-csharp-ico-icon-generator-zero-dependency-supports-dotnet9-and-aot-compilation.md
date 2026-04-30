---
title: "SharpIco：用纯C#打造零依赖的.ico图标生成器，支持.NET9与AOT编译"
slug: sharpico-pure-csharp-ico-icon-generator-zero-dependency-supports-net9-and-aot-compilation
description: "网上搜到的很多 ICO 制作工具都是针对 favicon 的，其他的要不太重，要不就是收费，于是我把目光重新放在了开源工具上"
date: 2025-05-27 19:55:40
lastmod: 2025-05-27 20:27:17
author: 程序设计实验室
originalTitle: "SharpIco：用纯C#打造零依赖的.ico图标生成器，支持.NET9与AOT编译"
originalLink: https://mp.weixin.qq.com/s/nDPbEESRN215DzimV12Lgw
copyright: Reprinted
draft: false
cover: https://img1.dotnet9.com/2025/05/cover_02.png
banner: false
categories:
  - .NET
albums:
  - C# AOT
tags:
  - .NET
  - C#
  - 开源
  - AOT
  - ICO
---

## 前言

最近一直在完善我今年的两款桌面软件：[视频剪辑工具 Clipify](https://blog.deali.cn/Blog/Post/6a903b1c6fb2487f) 和 [AI 文章创作工具 StarBlogPublisher](https://blog.deali.cn/p/starblog-extrapolation-4-publisher-major-upgrade)

虽然界面是基本完善了，但图标还是默认的，显得很不专业

于是我打算给这俩软件换个好看的图标

早在 VB6.0 年代，我用过一款开源的 ICO 图标制作工具，不过现在已经找不到了

网上搜到的很多 ICO 制作工具都是针对 favicon 的

其他的要不太重，要不就是收费，于是我把目光重新放在了开源工具上

找到了一个基于 nodejs 实现并且零依赖的（大部分都是依赖于 Magick 这个 C++实现的图片库），然而很遗憾，这个工具在我电脑上并不能使用……

## 目标

这时我想起来之前曾经用 c# 开发了一个图片格式转换工具，使用了 .Net8 的 AOT 功能，可以像 go 语言那样开发出跨平台的单可执行文件

所以我决定继续使用 C# 开发一个图标生成工具，这个工具可以实现：

- 纯 C# 实现，没有外部依赖，跨平台，单可执行文件，AOT
- 将 png 图片分解为多张不同尺寸的小图片(边长 16, 32, 48, 64, 128, 256, 512)，然后合成一张 ICO 图标，实现不同尺寸屏幕的良好视觉体验
- 支持 Inspect 功能，可以读取并分析 ICO 图标
- 方便的分发方式，支持 dotnet tool, scoop, brew 等工具一键安装

目前已经完成了，并且也发布到了 nuget 和 scoop，接下来再研究下如何发布到 brew

项目主页: https://github.com/star-plan/sharp-ico

## 实现

在 SharpIco 中，`.ico` 文件的生成完全不依赖 ImageMagick 或任何图像处理外部工具，而是通过纯 C# 代码手工拼接符合规范的 ICO 二进制结构。

这部分的核心类是 `IcoGenerator`，具体代码我就不贴了，在项目里有，挑几个要点介绍吧~

### 生成多尺寸图像

使用 ImageSharp 生成多尺寸图像

```csharp
var clone = original.Clone(ctx => ctx.Resize(size, size));
clone.SaveAsPng(ms);
```

- **使用 ImageSharp 的 Resize 与 Clone** 功能，将原始高分辨率 PNG 生成多个目标尺寸（如 16x16、32x32、256x256）
- 以 **PNG 格式保存到内存流中**，用于后续写入 `.ico`

> 💡 ICO 文件支持嵌入 PNG 图像（从 Vista 开始），这样可以保持更小体积与更佳透明度效果。

### 手动构建 ICO 文件头

按照 ICO 文件格式手动构建 ICONDIR 与 ICONDIRENTRY

ICO 文件的头部由 3 部分组成：

- `ICONDIR`（6 字节）：固定结构
- `ICONDIRENTRY × N`（每个 16 字节）：描述每一张嵌入图像的尺寸、偏移
- `Image Data × N`：实际图像二进制数据

```csharp
writer.Write((ushort)0); // Reserved
writer.Write((ushort)1); // Type = icon
writer.Write((ushort)images.Count); // Image count
```

- 先写入 ICO 文件头 ICONDIR
- 然后循环写入每一张图片的描述信息（宽高、位深、数据偏移等）

```csharp
writer.Write((byte)(img.Width == 256 ? 0 : img.Width)); // 256 用 0 表示
writer.Write((ushort)32); // bits per pixel
writer.Write(image.Length);
writer.Write(offset);
```

**ICO 文件中的宽度/高度字段如果为 0，表示 256**。这是 ICO 格式的一个特殊规定。

> ICO 文件格式在表示图像尺寸时有一个限制：宽度和高度字段各只有一个字节，值范围是 0-255。当这些字段为 0 时，按照规范表示 256 像素。对于大于 256 的尺寸（如 512×512 或 1024×1024），在文件头中仍然会显示为 0（即 256），但实际图像数据可以包含更大尺寸的图像。

### 拼接图像

拼接所有 PNG 图像数据

```csharp
foreach (var image in images) {
    writer.Write(image);
}
```

- 所有描述信息写完后，紧跟着写入图像数据本体
- 由于提前计算了偏移量，确保每张图像数据可以被系统正确识别和读取

### 扩展

支持自定义尺寸

```csharp
public static void GenerateIcon(string sourcePng, string outputIco, int[] sizes)
```

- 默认支持 16~512 的常见尺寸
- 可通过传参灵活指定尺寸组合（比如只打包 32/256）

## Inspect 功能

除了图标生成，SharpIco 还内置了一个图标内容分析工具 `IcoInspector`，可以帮助开发者深入理解 `.ico` 文件内部结构，并验证实际包含的图层图像尺寸与位深信息，解决市面上不少图标工具生成不规范 `.ico` 文件的问题。

这里简单介绍一下实现思路~

### 读取 ICO 文件头

手动读取 ICONDIR + ICONDIRENTRY 结构

ICO 文件的头部结构由三个部分组成：

- `ICONDIR`（6 字节）：标记为图标、记录图像数量
- `ICONDIRENTRY × N`（每个 16 字节）：记录每张图片的元信息（宽、高、位深、数据偏移等）
- 图像数据块：实际的 PNG 或 BMP 图像

```csharp
ushort reserved = reader.ReadUInt16(); // 必须为0
ushort type = reader.ReadUInt16();     // 1表示图标
ushort count = reader.ReadUInt16();    // 图像数量
```

随后逐个读取图像条目，保存到内存中用于后续处理。

```csharp
byte width = reader.ReadByte();
byte height = reader.ReadByte();
ushort bitCount = reader.ReadUInt16();
int sizeInBytes = reader.ReadInt32();
int imageOffset = reader.ReadInt32();
```

对了，前面介绍过，ICO 头部的空间有限，只能存 8 位，所以如果 width/height 字段为 `0`，根据规范表示 256，或者是超过 256

### 提取&解析图像数据

提取图像数据，校验真实分辨率

ICO 文件中记录的宽高未必真实，尤其是嵌入 PNG 格式的情况。因此，使用 `ImageSharp` 对每张图像进行真正解析。

```csharp
fs.Seek(entry.ImageOffset, SeekOrigin.Begin);
fs.Read(imageData, 0, dataSize);
Image.Load(imageData) → 获取真实 Width 和 Height
```

- 通过 `GetImageDimensions()` 方法判断是否为 PNG，并用 `ImageSharp` 加载
- 如果格式错误或读取失败，回退为头部声明的尺寸

这一点可用于检测某些“伪造 ICO”的问题（比如尺寸与图像内容不一致）

### 补全并输出分析结果

所有条目都读取并解析后，将内容输出为结构化信息：

```yaml
正在检查ICO文件: logo.ico
图标数量: 7
- 第1张图像: 16x16, 32bpp, 大小: 840字节, 偏移: 118
- 第2张图像: 32x32, 32bpp, 大小: 1939字节, 偏移: 958
- 第3张图像: 48x48, 32bpp, 大小: 3375字节, 偏移: 2897
- 第4张图像: 64x64, 32bpp, 大小: 4951字节, 偏移: 6272
- 第5张图像: 128x128, 32bpp, 大小: 13782字节, 偏移: 11223
- 第6张图像: 256x256, 32bpp, 大小: 37823字节, 偏移: 25005
- 第7张图像: 512x512, 32bpp, 大小: 114655字节, 偏移: 62828
  注意: 文件头中指定的尺寸为256x256，但实际图像尺寸为512x512
```

这样能够快速确认：

- 一个 `.ico` 文件中包含多少图层
- 每层的真实尺寸与位深
- 是否存在格式问题（尺寸对不上、大小异常等）

## 命令行界面设计

SharpIco 不只是一个代码库，同时也提供了完整的命令行工具，方便在任意开发场景中快速调用，无论是手动使用还是集成进构建脚本都毫无压力。

这一部分使用了 .NET 的现代 CLI 构建库 [`System.CommandLine`](https://github.com/dotnet/command-line-api)，实现了两个主命令：

- `generate`：将 PNG 图像转换为 ICO 图标
- `inspect`：检查 ICO 文件的结构与图层信息

命令行使用方法：

```bash
sharpico generate -i logo.png -o icon.ico --sizes 16 32 48 256
sharpico inspect icon.ico
```

### 🛠️ 生成命令 `generate`

这个命令支持通过参数控制输入、输出路径和生成图标的尺寸：

```bash
sharpico generate --input logo.png --output app.ico --sizes 16 32 64 256
```

#### 参数说明：

| 参数       | 简写 | 说明                                                 |
| ---------- | ---- | ---------------------------------------------------- |
| `--input`  | `-i` | 源 PNG 图像路径（必须）                              |
| `--output` | `-o` | 输出 ICO 文件路径（必须）                            |
| `--sizes`  | `-s` | 要生成的图标尺寸列表，支持多个（默认为常见七种尺寸） |

> 支持多个尺寸同时生成，内部调用 `IcoGenerator.GenerateIcon()` 实现多图层 `.ico` 文件创建。

举个例子：

```bash
sharpico generate -i logo.png -o icon.ico -s 32 64 256
```

输出将包含三种分辨率图层。

### 🔍 检查命令 `inspect`

可以对任意 `.ico` 文件进行结构检查与验证：

```bash
sharpico inspect icon.ico
```

这会输出图标中每一张图层的：

- 宽高（声明与实际尺寸）
- 位深（bpp）
- 数据偏移与大小
- 是否存在头部与实际图像尺寸不一致的潜在问题

示例输出：

```yaml
正在检查ICO文件: logo.ico
图标数量: 7
- 第1张图像: 16x16, 32bpp, 大小: 840字节, 偏移: 118
- 第2张图像: 32x32, 32bpp, 大小: 1939字节, 偏移: 958
- 第3张图像: 48x48, 32bpp, 大小: 3375字节, 偏移: 2897
- 第4张图像: 64x64, 32bpp, 大小: 4951字节, 偏移: 6272
- 第5张图像: 128x128, 32bpp, 大小: 13782字节, 偏移: 11223
- 第6张图像: 256x256, 32bpp, 大小: 37823字节, 偏移: 25005
- 第7张图像: 512x512, 32bpp, 大小: 114655字节, 偏移: 62828
  注意: 文件头中指定的尺寸为256x256，但实际图像尺寸为512x512
```

## 发布

SharpIco 不只是一个库，也不是一个只能源码使用的小工具，而是一个可以 **通过 `dotnet tool` 全平台安装运行**、**支持 AOT 编译优化性能和体积** 的专业图标工具。

这次需要同时支持 AOT 和传统发布，所以对项目文件做了一些配置

```xml
<Project Sdk="Microsoft.NET.Sdk">

    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>net9.0</TargetFramework>
        <ImplicitUsings>enable</ImplicitUsings>
        <Nullable>enable</Nullable>
        <!-- AOT编译设置移至条件属性组 -->
        <InvariantGlobalization>true</InvariantGlobalization>

        <!-- .NET Tool 配置 -->
        <PackAsTool>true</PackAsTool>
        <ToolCommandName>sharpico</ToolCommandName>
        <PackageOutputPath>./nupkg</PackageOutputPath>

        <!-- 包信息 -->
        <PackageId>SharpIco</PackageId>
        <Version>1.0.0</Version>
        <Authors>StarPlan</Authors>
        <Description>SharpIco是一个纯 C# AOT 实现的轻量级图标生成工具，用于生成和检查ICO图标文件。可将一张高分辨率 PNG 图片一键生成标准的 Windows .ico 图标文件，内含多种尺寸（16x16 到 512x512），还可以自定义尺寸。除了图标生成，SharpIco 还内置图标结构分析功能，助你轻松验证 .ico 文件中包含的图层与尺寸。</Description>
        <PackageTags>icon;ico;png;converter,DealiAxy,cli,tool,dotnet-tool,imagesharp</PackageTags>
        <PackageProjectUrl>https://github.com/star-plan/sharp-ico</PackageProjectUrl>
        <RepositoryUrl>https://github.com/star-plan/sharp-ico</RepositoryUrl>
        <PackageLicenseExpression>MIT</PackageLicenseExpression>
        <PackageReadmeFile>README.md</PackageReadmeFile>
    </PropertyGroup>

    <!-- AOT发布专用设置 -->
    <PropertyGroup Condition="'$(PublishAot)' == 'true'">
        <PublishAot>true</PublishAot>
        <TrimMode>full</TrimMode>
        <InvariantGlobalization>true</InvariantGlobalization>
        <IlcGenerateStackTraceData>false</IlcGenerateStackTraceData>
        <IlcOptimizationPreference>Size</IlcOptimizationPreference>
        <IlcFoldIdenticalMethodBodies>true</IlcFoldIdenticalMethodBodies>
        <JsonSerializerIsReflectionEnabledByDefault>true</JsonSerializerIsReflectionEnabledByDefault>
    </PropertyGroup>

    <ItemGroup>
        <None Include="README.md" Pack="true" PackagePath="\" />
    </ItemGroup>

    <ItemGroup>
        <PackageReference Include="SixLabors.ImageSharp" Version="3.1.8" />
        <PackageReference Include="System.CommandLine" Version="2.0.0-beta4.22272.1" />
    </ItemGroup>

</Project>
```

几个要点：

### 定义为 CLI 工具（dotnet tool）

```xml
<PackAsTool>true</PackAsTool>
<ToolCommandName>sharpico</ToolCommandName>
```

这使得 SharpIco 可以像任何其他 `.NET CLI 工具` 一样被全局安装：

```bash
dotnet tool install -g SharpIco --add-source ./nupkg
```

安装后，只需运行：

```bash
sharpico generate -i logo.png -o icon.ico
```

### 支持 AOT 编译发布（.NET 9 原生支持）

```xml
<PublishAot>true</PublishAot>
<IlcOptimizationPreference>Size</IlcOptimizationPreference>
<TrimMode>full</TrimMode>
```

通过设置 PublishAot=true，SharpIco 支持编译为原生可执行文件

无需 .NET 运行时支持，启动速度极快，适合构建工具链或集成环境使用

AOT 构建命令示例：

```bash
dotnet publish -c Release -r win-x64 /p:PublishAot=true
```

生成的 `sharpico.exe` 是一个纯原生的 Windows 可执行文件，无需安装 .NET！

> 除 Windows 外，也可发布为 `linux-x64`, `osx-arm64` 等跨平台目标。

## 自动发布 nuget

SharpIco 采用了完整的 GitHub Actions CI/CD 流水线，做到**一次打 tag，全平台构建自动完成**。

只需推送一个符合语义化格式的标签（如 `v1.0.0`），系统将自动：

1. 构建并发布 NuGet 工具包
2. 针对 Windows / Linux / macOS 编译原生 AOT 可执行文件
3. 自动上传所有产物到 GitHub Release 页面

我之前已经写过关于如何发布 nuget 的文章了，详见：

- [开发现代化的.NetCore控制台程序 (3) 将nuget包发布到GitHubPackages](https://blog.deali.cn/Blog/Post/977b511be1ca5f43)
- [开发现代化的.NetCore控制台程序 (4) 使用GithubAction自动构建以及发布nuget包](https://blog.deali.cn/Blog/Post/36dc21f5652f8ff4)

这部分问题不大

不过这次还有些不一样，之前发布的是类库和项目模板，这次是 dotnet tool

这类似于 npx 脚本、pip 工具之类的概念

可以使用 dotnet tool 命令安装和调用

不过这种方式就不能使用 AOT，只能使用 framework dependant 方式发布

nuget 发布比较简单，也是使用 dotnet 命令

```bash
dotnet pack -c Release
```

不过关键在于我要用 GitHub Action 来自动化构建和发布，这个流程和之前差不多

```yaml
name: 发布SharpIco
run-name: ${{ github.actor }} 正在发布SharpIco 🚀

on:
  push:
    tags:
      - "v*.*.*"  # 更明确的版本格式匹配

# 为整个工作流设置权限
permissions:
  contents: write
  id-token: write
  issues: write

jobs:
  # 第一步：发布NuGet包
  publish-nuget:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0  # 获取所有历史记录用于版本号计算
      
      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: 9.0.x
      
      - name: 缓存NuGet包
        uses: actions/cache@v3
        with:
          path: ~/.nuget/packages
          key: ${{ runner.os }}-nuget-${{ hashFiles('**/packages.lock.json') }}
          restore-keys: |
            ${{ runner.os }}-nuget-
      
      - name: 提取版本号
        id: get_version
        shell: bash
        run: echo "VERSION=${GITHUB_REF#refs/tags/v}" >> $GITHUB_OUTPUT
      
      - name: 恢复依赖
        run: dotnet restore ./SharpIco/SharpIco.csproj
      
      - name: 运行测试
        run: dotnet test --no-restore

      - name: 构建项目
        run: dotnet build --no-restore -c Release --nologo ./SharpIco/SharpIco.csproj -p:Version=${{ steps.get_version.outputs.VERSION }}
      
      - name: 创建NuGet包
        run: dotnet pack -c Release ./SharpIco/SharpIco.csproj -p:PackageVersion=${{ steps.get_version.outputs.VERSION }} --no-build --output ./nupkg
        
      - name: 发布到NuGet Gallery
        run: dotnet nuget push ./nupkg/*.nupkg --api-key ${{ secrets.NUGET_GALLERY_TOKEN }} --source https://api.nuget.org/v3/index.json --skip-duplicate
```

## 自动发布到 Github Release

这个是最折腾的，我反复调试了十几次才成功😂

不得不说 Github Action 的调试太不友好了

直接贴上我最后成功的配置吧

```yaml
name: 发布SharpIco
run-name: ${{ github.actor }} 正在发布SharpIco 🚀

on:
  push:
    tags:
      - "v*.*.*"  # 更明确的版本格式匹配

# 为整个工作流设置权限
permissions:
  contents: write
  id-token: write
  issues: write

jobs:
  # 第二步：编译各平台可执行文件
  build-executables:
    needs: publish-nuget  # 确保在NuGet包发布后运行
    strategy:
      fail-fast: false
      matrix:
        kind: ['windows', 'linux', 'macOS']
        include:
          - kind: windows
            os: windows-latest
            target: win-x64
            extension: '.zip'
          - kind: linux
            os: ubuntu-latest
            target: linux-x64
            extension: '.tar.gz'
          - kind: macOS
            os: macos-latest
            target: osx-x64
            extension: '.tar.gz'

    runs-on: ${{ matrix.os }}
    
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0  # 获取所有历史记录用于版本号计算
      
      - name: 提取版本号
        id: get_version
        shell: bash
        run: echo "VERSION=${GITHUB_REF#refs/tags/v}" >> $GITHUB_OUTPUT
      
      - name: Setup .NET
        uses: actions/setup-dotnet@v3
        with:
          dotnet-version: 9.0.x
      
      - name: 缓存NuGet包
        uses: actions/cache@v3
        with:
          path: ~/.nuget/packages
          key: ${{ runner.os }}-nuget-${{ hashFiles('**/packages.lock.json') }}
          restore-keys: |
            ${{ runner.os }}-nuget-
      
      - name: 安装Linux依赖
        if: matrix.kind == 'linux'
        run: |
          sudo apt-get update
          sudo apt-get install -y clang zlib1g-dev libkrb5-dev

      - name: 设置Windows环境
        if: matrix.kind == 'windows'
        shell: pwsh
        run: |
          Write-Host "设置Windows编译环境..."
          # 确保有最新的开发者工具
          choco install visualstudio2022buildtools -y --no-progress
      
      - name: 恢复依赖
        run: dotnet restore ./SharpIco/SharpIco.csproj
      
      - name: AOT编译
        run: |
          echo "正在为 ${{ matrix.kind }} 平台进行AOT编译..."
          dotnet publish ./SharpIco/SharpIco.csproj -c Release -r ${{ matrix.target }} --self-contained true -p:PublishAot=true -p:Version=${{ steps.get_version.outputs.VERSION }} -o ./publish/${{ matrix.kind }}
      
      - name: 打包Windows可执行文件
        if: matrix.kind == 'windows'
        run: |
          cd ./publish/${{ matrix.kind }}
          7z a -tzip ../../SharpIco-${{ matrix.kind }}-${{ steps.get_version.outputs.VERSION }}${{ matrix.extension }} *
      
      - name: 打包Linux/macOS可执行文件
        if: matrix.kind != 'windows'
        run: |
          cd ./publish/${{ matrix.kind }}
          tar -czvf ../../SharpIco-${{ matrix.kind }}-${{ steps.get_version.outputs.VERSION }}${{ matrix.extension }} *
      
      # 上传构建产物作为工作流构件(artifacts)
      - name: 上传构建产物
        uses: actions/upload-artifact@v4
        with:
          name: SharpIco-${{ matrix.kind }}-${{ steps.get_version.outputs.VERSION }}
          path: ./SharpIco-${{ matrix.kind }}-${{ steps.get_version.outputs.VERSION }}${{ matrix.extension }}
          retention-days: 1

  # 第三步：统一上传所有平台可执行文件到GitHub Release
  upload-to-release:
    needs: build-executables
    runs-on: ubuntu-latest
    
    steps:
      - name: 提取版本号
        id: get_version
        shell: bash
        run: echo "VERSION=${GITHUB_REF#refs/tags/v}" >> $GITHUB_OUTPUT
      
      # 下载所有平台构建产物
      - name: 下载Windows构建产物
        uses: actions/download-artifact@v4
        with:
          name: SharpIco-windows-${{ steps.get_version.outputs.VERSION }}
          path: ./artifacts
      
      - name: 下载Linux构建产物
        uses: actions/download-artifact@v4
        with:
          name: SharpIco-linux-${{ steps.get_version.outputs.VERSION }}
          path: ./artifacts
      
      - name: 下载macOS构建产物
        uses: actions/download-artifact@v4
        with:
          name: SharpIco-macOS-${{ steps.get_version.outputs.VERSION }}
          path: ./artifacts
      
      # 列出下载的文件以确认
      - name: 列出下载的文件
        run: ls -la ./artifacts
      
      # 统一上传到GitHub Release
      - name: 上传所有文件到GitHub Release
        uses: softprops/action-gh-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          files: ./artifacts/*
          tag_name: ${{ github.ref }}
          fail_on_unmatched_files: false
          draft: false
          name: SharpIco 版本 ${{ steps.get_version.outputs.VERSION }}
          generate_release_notes: true 
```

### 工作流总览

整个工作流由三大 Job 组成：

| 阶段                | 描述                                           |
| ------------------- | ---------------------------------------------- |
| `publish-nuget`     | 编译项目并发布 `.nupkg` 包到 NuGet.org         |
| `build-executables` | 为三大平台编译 AOT 原生可执行文件              |
| `upload-to-release` | 将所有产物上传到当前 tag 对应的 GitHub Release |

发布 nuget 部分比较容易，这里就不重复了

### 编译三平台 AOT 可执行文件（build-executables）

采用 GitHub Matrix 构建策略，分别针对：

- `win-x64`（Windows 可执行文件，ZIP 打包）
- `linux-x64`（Linux ELF，可执行，tar.gz 打包）
- `osx-x64`（macOS 可执行，tar.gz 打包）

```bash
dotnet publish -c Release -r ${{ matrix.target }} --self-contained true -p:PublishAot=true
```

每个平台生成对应压缩包，打包后使用 `upload-artifact` 上传中转。

### 上传至 GitHub Release（upload-to-release）

这一阶段会自动下载之前构建的产物，并打包进当前版本的 Release 页面：

```bash
uses: softprops/action-gh-release@v1
```

发布页面自动生成更新说明（`generate_release_notes: true`），方便用户查看版本变更。

最终效果如下图所示：

```python
🔖 v1.0.0
├── SharpIco-windows-v1.0.0.zip
├── SharpIco-linux-v1.0.0.tar.gz
└── SharpIco-macOS-v1.0.0.tar.gz
```

### 坑

这里最大的坑就是原本使用 `actions/upload-artifact@v3` 一直报错，后面查了 issue 发现是已经 deprecate 了

升级到 v4 问题解决

另外就是发布 GitHub Release 时老是冲突，一直说已存在

设置了 append 也不行……

后面我改成先上传中转，后面一次性发布，才终于成功了😂

## 小结

`SharpIco` 是我基于 .NET 9 和 AOT 编译能力打造的一款纯 C# 图标工具，目标是**替代繁琐依赖、简化图标生成与验证流程**。

从 `ImageSharp` 图像处理，到 `BinaryWriter` 手工拼装 ICO 格式；从 `System.CommandLine` 打造命令行体验，到 GitHub Actions 全流程自动化发布；SharpIco 不仅是一个实用工具，也是一场小而美的工程探索。

它代表了我对工具理想的追求：**轻量、纯粹、易集成、跨平台、即开即用**。

- 无需安装 Python、Node 或 ImageMagick
- 支持 AOT 编译，生成原生可执行文件
- 同时具备图标生成与结构分析功能
- 一次发布，自动产出全平台构建结果

欢迎 Star / Fork / Issue / PR，欢迎将它集成进你的构建系统中。

📦 GitHub 项目地址：
👉 https://github.com/star-plan/sharp-ico
