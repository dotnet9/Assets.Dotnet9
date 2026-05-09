# Assets.Dotnet9

![Assets.Dotnet9](https://img1.dotnet9.com/site/doc/website/imgs/assets-dotnet9.svg)

`Assets.Dotnet9` 是 CodeWF 网站的资源与内容仓库。它不只是图片目录，而是承担了文件型内容库的角色：文章、项目文档、工具元数据、时间线、友情链接、站点 Markdown 页面和配图都在这里维护。

## 适合关注

- 想了解 `dotnet9.com` 如何把内容数据从 Web 应用源码中分离出来。
- 想按年份、月份、栏目和项目目录管理 Markdown 与图片资源。
- 想给 CodeWF 网站新增文章、项目说明、工具入口或站点配置。

## 目录结构

```text
2019/ ... 2026/      按年份、月份组织文章和配图
site/
  about.md           关于页内容
  Privacy.md         隐私页内容
  albums.json        专题元数据
  categories.json    分类元数据
  friend-links.json  友情链接元数据
  timelines.json     时间线数据
  doc/               项目中心导航、正文和 SVG 封面
  tools/             在线工具元数据与预览图
  pays/              打赏页资源
  banners/           页面横幅资源
  favicon/           站点图标资源
```

## 内容维护原则

| 原则 | 说明 |
| --- | --- |
| 路径稳定 | 文章、图片、文档和工具 slug 尽量长期不变，避免历史链接失效。 |
| 元数据完整 | 文章 Front Matter、分类、专题、项目导航和工具 JSON 需要同步维护。 |
| 命名清晰 | 图片、SVG 和 Markdown 使用语义化文件名，便于搜索和复用。 |
| 可审阅 | 内容变更以文本文件为主，适合 Git diff 和 Pull Request 审阅。 |

## 与 CodeWF 的关系

`CodeWF` 负责网站程序，`Assets.Dotnet9` 负责内容和资源。应用启动时读取 `Site:LocalAssetsDir`，并把本仓库内容加载到项目中心、博客、工具、搜索和站点地图中。

开发时建议两个仓库并排克隆：

```text
CodeWF/
Assets.Dotnet9/
```

## 仓库

- 资源仓库：[https://github.com/dotnet9/Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9)
- 网站源码：[https://github.com/dotnet9/CodeWF](https://github.com/dotnet9/CodeWF)
