# Assets.Dotnet9 内容规范

本仓库是 CodeWF 网站的内容源。

English version: [CONTENT_GUIDE.md](./CONTENT_GUIDE.md)

## 1. 文章

推荐路径：

```text
YYYY/MM/slug.md
```

推荐 Front Matter：

```yaml
---
title: 示例标题
slug: example-slug
description: 用于列表摘要和 SEO 的一句话简介。
date: 2026-04-28 20:00:00
lastmod: 2026-04-28 20:00:00
cover: https://img1.dotnet9.com/2026/04/example-cover.png
banner: false
categories:
  - .NET
albums:
  - C#开源项目
tags:
  - Native AOT
  - Razor Pages
author: Dotnet9
draft: false
---
```

发布级文章建议至少包含：

- `title`
- `slug`
- `description`
- `date`
- `categories`

强烈建议补齐：

- `lastmod`
- `cover`
- `albums`
- `tags`
- `author`
- `banner`

## 2. Slug 规则

- 使用小写字母、数字和连字符。
- 文章发布后尽量不要修改 slug。
- 单个 slug 内避免中英文混杂。
- 优先使用可读名称，不要依赖纯编号文件名。
- 文章 `categories` 和 `albums` 使用 `site/categories.json`、`site/albums.json` 中的展示名称。
- 文章标签保持聚焦，每篇最多 5 个。

## 3. 封面与图片规则

- 每篇文章尽量提供一张明确的封面图。
- 文件名尽量有语义，例如 `razor-pages-search-cover.png`。
- 按资源类型优先使用 `png`、`jpg`、`webp` 或 `svg`。
- 图示、封面尽量保持清晰、干净，并适合列表卡片展示。

## 4. 站点级数据

`site/` 下的文件会被当成导航级或产品级数据使用。

常见文件：

- `albums.json`
- `categories.json`
- `friend-links.json`
- `timelines.json`
- `doc/navigation.json`
- `tools/tools.json`

修改这些文件时建议遵守：

- key 保持稳定
- 排序有明确意图
- 避免重复 slug
- memo 文案保持简洁，并能直接服务 UI 展示

## 5. 文档区

文档区由以下内容驱动：

- `site/doc/navigation.json`
- `site/doc/` 下的 markdown 文件

建议规则：

- 导航 slug 与 markdown 文件名保持一致
- 章节结构尽量浅、尽量清晰
- 只有在确实能提升理解时再加入图片

## 6. 仓库整洁度

- 不提交密钥或敏感信息。
- 不提交临时导出文件或本地缓存。
- 避免提交操作系统生成的无关文件。
- 跨年份保持一致的命名风格。

## 7. 提交前检查

合并内容更新前，建议确认：

- slug 正确
- 摘要存在
- 分类与专题归属一致
- 封面在列表视图中可正常展示
- 内链和图片地址可访问
- 如有需要，时间线或 `site/*.json` 已同步更新
