# CodeWF

![CodeWF](https://img1.dotnet9.com/site/doc/website/imgs/codewf.svg)

`CodeWF` 是 `dotnet9.com` / `codewf.com` 的网站源码仓库，当前站点基于 ASP.NET Core Razor Pages 构建。它把同级的 `Assets.Dotnet9` 作为文件型内容仓库使用，运行时读取文章、项目文档、工具元数据、时间线、友情链接和站点级 Markdown 页面。

## 适合关注

- 想了解 `dotnet9.com` 网站如何组织 Razor Pages、内容服务、搜索和站点地图。
- 想把 Markdown、JSON 和图片资源从应用源码中拆出来，形成轻量 CMS 式维护流程。
- 想参考一个以 .NET 为主、同时包含博客、项目中心和在线工具的网站工程。

## 核心职责

| 模块 | 说明 |
| --- | --- |
| `src/WebApp/Pages` | Razor Pages 页面，包括首页、文章、项目中心、工具页、搜索页等。 |
| `src/WebApp/Services/AppService.cs` | 统一加载资源仓库内容，并维护内存缓存、搜索索引和站点地图。 |
| `src/WebApp/Models` | 文章、分类、专题、项目、工具、搜索结果等内容模型。 |
| `tests/WebApp.Tests` | 最小测试集，覆盖 Front Matter、Markdown 渲染和搜索关键词拦截。 |

## 与资源仓库协作

站点通过 `Site:LocalAssetsDir` 指向本地 `Assets.Dotnet9` 目录。开发环境下，文件监听会在 Markdown、JSON、图片或 SVG 变化时清空内容缓存，所以维护资源仓库后刷新页面即可看到结果。

常见输入包括：

- `site/doc/navigation.json`
- `site/tools/tools.json`
- `site/categories.json`
- `site/albums.json`
- `site/timelines.json`
- `site/about.md`
- `2019/` 到当前年份的文章目录

## 本地运行

```powershell
dotnet run --project src/WebApp/WebApp.csproj
```

如果资源仓库不在默认路径，可用环境变量覆盖：

```powershell
$env:Site__LocalAssetsDir = "D:\github\owner\Assets.Dotnet9"
dotnet run --project src/WebApp/WebApp.csproj
```

## 仓库

- 网站源码：[https://github.com/dotnet9/CodeWF](https://github.com/dotnet9/CodeWF)
- 内容资源：[https://github.com/dotnet9/Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9)
