# Assets.Dotnet9 内容规范

本仓库是 CodeWF 网站的内容源。

仓库说明：[中文](./README-zh_CN.md) / [English](./README.md)

## 1. 文章

本站按“中文主站”维护。英文、日文、繁中文件可以继续保留作为历史资产或按需补充，但新增内容不要求同步翻译；优先把中文文章、专题路线、项目说明和高频工具整理清楚。

文章路径与同目录配图：

```text
YYYY/MM/slug.md         内嵌 YAML 元数据与 Markdown 正文
YYYY/MM/slug-cover.png  文章封面
YYYY/MM/slug-demo.gif   操作动图（按需）
```

文章元数据统一使用 **Markdown 文件开头的内嵌 YAML front matter**，以两行独立的 `---` 包围，结束分隔符之后空一行再写正文。新建或更新文章时，元数据与正文维护在同一份 `.md` 中，不再另建同名 `.yml`。

示例（`YYYY/MM/example-slug.md`）：

```markdown
---
title: "示例标题"
slug: example-slug
description: "用于列表摘要和 SEO 的一句话简介。"
date: 2026-04-28 20:00:00
lastmod: 2026-04-28 20:00:00
cover: 2026/04/example-cover.png
banner: false
categories:
  - .NET
tags:
  - Native AOT
  - Razor Pages
author: dotnet9
draft: true
---

正文从这里开始。

![示例配图](example-cover.png)
```

- `title`、`slug`、摘要、分类、专题、标签、封面等信息统一保存在文件头，Vox 元数据面板也以这里的内容为准，避免与正文内另写的字段或独立 `.yml` 重复维护。
- 文件使用 UTF-8；YAML 缩进使用空格。`categories`、`albums`、`tags` 使用列表；`banner`、`draft` 使用布尔值 `true` / `false`，不要写成字符串或图片地址。
- 标题、摘要等含冒号、井号或其他 YAML 特殊字符时加引号，例如标签 `"C#"`。已有自定义字段应保留，不为统一格式丢失元数据。
- 日期采用 `YYYY-MM-DD HH:mm:ss`，`date` 记录文章日期，`lastmod` 记录实际更新日期。新稿保持 `draft: true`，验收并准备发布后再改为 `false`。
- 历史 sidecar 文件按需迁移：先核对字段并合入对应 `.md` 文件头，确认无丢失和冲突后再移除旧 `.yml`；不为一篇新稿批量迁移历史文章。

发布级文章建议至少包含：

- `title`
- `slug`
- `description`
- `date`
- `categories`
- `draft`

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
- 文章 `categories` 使用 `site/categories.json` 中的展示名称；启用专题且存在专题目录时，`albums` 使用对应目录中的展示名称。没有可核对的专题归属时可省略 `albums`。
- 文章标签保持聚焦，每篇最多 5 个。

## 3. 分类、专题与标签

- `categories` 只放粗粒度技术方向，例如 `.NET`、`C#`、`WPF`、`Avalonia UI`。
- `albums` 用来组织连续阅读路线，例如 `WPF UI设计`、`C#开源项目`、`从护士到C#开发者`。
- `tags` 用来记录细粒度关键词，例如 `Native AOT`、`Prism`、`Markdown`。
- 一篇文章可以没有专题，但如果它属于某条长期路线，优先补上 `albums`，这样文章详情页可以引导读者继续阅读。

## 4. 封面与图片规则

- 每篇文章尽量提供一张明确的封面图。
- 正文图片和动图优先与文章放在同一目录，使用相对 Markdown 文件的路径，例如 `![操作演示](example-demo.gif)`；文章文件头的 `cover` 使用仓库根相对路径（如 `2026/04/example-cover.png`），也可使用已可访问的 HTTPS 图片地址。
- 本地草稿不填写尚未上传的 CDN 地址。通过 Vox 发布时检查图片映射与目标平台的实际呈现，图片路径有效不等于文章已经发布。
- 文件名尽量有语义，例如 `razor-pages-search-cover.png`。
- 静态图片按需使用 `png`、`jpg`、`webp` 或 `svg`，连续操作演示可使用 `gif`；为图片写明与正文相关的替代文本。
- 图示、封面尽量保持清晰、干净，并适合列表卡片展示。

## 5. 站点级数据

`site/` 下的文件会被当成导航级或产品级数据使用。

常见文件：

- `albums.json`（启用专题目录时）
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

## 6. 文档区

文档区由以下内容驱动：

- `site/doc/navigation.json`
- `site/doc/` 下的 markdown 文件

建议规则：

- 导航 slug 与 markdown 文件名保持一致
- 章节结构尽量浅、尽量清晰
- 只有在确实能提升理解时再加入图片

## 7. 仓库整洁度

- 不提交密钥或敏感信息。
- 不提交临时导出文件或本地缓存。
- 避免提交操作系统生成的无关文件。
- 跨年份保持一致的命名风格。

## 8. 提交前检查

合并内容更新前，建议确认：

- slug 正确
- 内嵌 YAML 可解析、字段类型正确，未重复维护同名 `.yml`
- 摘要存在
- 分类与专题归属一致
- 封面在列表视图中可正常展示
- 内链和图片地址可访问
- 待验收文章保留 `draft: true`，正式发布前复核文章日期与 `lastmod`
- 如有需要，时间线或 `site/*.json` 已同步更新

## 9. 转载内容

- 获得明确授权的文章，应在正文开头写明“经原作者授权转载”，并列出作者、首发平台和原文链接。
- 仅能确认来源、不能确认授权状态的历史转载，不得补写“经授权”等表述；只说明转载属性、原作者和原文链接。
- 文件头内嵌 YAML 应包含 `author`、`originalTitle`、`originalLink` 和 `copyright: Reprinted`。
- 转载正文只做 Markdown 排版、代码块修复和图片本地化，不静默修改原文观点或技术结论。
- 第三方转载文章和媒体不自动适用仓库根目录的 MIT 许可证，具体边界见 [NOTICE.md](./NOTICE.md)。
