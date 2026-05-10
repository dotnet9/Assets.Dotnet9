# Assets.Dotnet9 Content Guide

This repository is the content source for the CodeWF website.

## 1. Articles

Recommended path:

```text
YYYY/MM/slug.md   Article body
YYYY/MM/slug.yml  Article metadata
```

Recommended sidecar metadata (`YYYY/MM/slug.yml`):

```yaml
title: Example title
slug: example-slug
description: One-sentence summary for list pages and SEO.
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
```

The matching `.md` file should contain only the article body. Keep metadata in `.yml` so article text and listing/SEO data can be reviewed independently.

Required fields for publish-ready articles:

- `title`
- `slug`
- `description`
- `date`
- `categories`

Strongly recommended:

- `lastmod`
- `cover`
- `albums`
- `tags`
- `author`
- `banner`

## 2. Slug Rules

- Use lowercase letters, numbers, and hyphens.
- Keep slugs stable after publishing.
- Avoid language mixing inside a single slug.
- Use human-readable names instead of numbered filenames.
- Article `categories` and `albums` should use display names from `site/categories.json` and `site/albums.json`.
- Keep article tags focused; use at most five tags per article.

## 3. Cover and Image Rules

- Prefer one clear cover image per article.
- Use descriptive filenames such as `razor-pages-search-cover.png`.
- Prefer `png`, `jpg`, `webp`, or `svg` depending on the asset.
- Keep diagrams and covers visually clean and sized for list cards.

## 4. Site-Level Data

Files under `site/` are treated as navigation or product-level data.

Common files:

- `albums.json`
- `categories.json`
- `friend-links.json`
- `timelines.json`
- `doc/navigation.json`
- `tools/tools.json`

When changing these files:

- keep keys stable
- sort intentionally
- avoid duplicate slugs
- keep memo fields concise and useful for UI

## 5. Docs

Docs are driven by:

- `site/doc/navigation.json`
- markdown files in `site/doc/`

Rules:

- navigation slug and markdown filename should match
- keep section structure shallow and readable
- use images only when they materially improve understanding

## 6. Repository Hygiene

- Do not commit secrets.
- Do not commit temp exports or local cache files.
- Avoid OS-specific junk files.
- Keep naming consistent across years.

## 7. Review Checklist

Before merging content updates, verify:

- slug is correct
- summary is present
- categories and albums are consistent
- cover works in list view
- internal links and images resolve
- timeline or site json is updated if needed
