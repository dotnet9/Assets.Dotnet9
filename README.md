# Assets.Dotnet9

CodeWF site content and asset repository.

Chinese version: [README-zh_CN.md](./README-zh_CN.md)

This repository is not only for images. It acts as the file-based content source for the CodeWF website and stores:

- Blog posts
- Documentation pages
- Tool metadata
- Timeline data
- Friend links
- Site-level markdown pages
- Static images used by articles and site sections

## Related Repositories

- Website source: [CodeWF](https://github.com/dotnet9/CodeWF)
- Asset source: [Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9)

## Repository Layout

```text
2019/ ... 2026/      Year/month article bodies, sidecar metadata, and article images
site/
  about.md           About page
  Privacy.md         Privacy page content
  albums.json        Blog album metadata
  categories.json    Blog category metadata
  friend-links.json  Friend link metadata
  timelines.json     Timeline data
  doc/               Documentation navigation and markdown content
  tools/             Tool metadata and preview images
  pays/              Donation page assets
  banners/           Page banner assets
  favicon/           Shared site icons
```

## Role in Production

The web app reads this repository from a local filesystem path configured with `Site:LocalAssetsDir`.

That means this repository should be maintained with the same level of discipline as a CMS content database:

- stable paths
- predictable metadata
- reviewable markdown
- reusable asset naming
- documented maintenance rules

## Content Standards

- English: [CONTENT_GUIDE.md](./CONTENT_GUIDE.md)
- 中文： [CONTENT_GUIDE-zh_CN.md](./CONTENT_GUIDE-zh_CN.md)

## Maintenance Notes

- Keep article bodies under `YYYY/MM/slug.md`.
- Keep article metadata under matching `YYYY/MM/slug.yml` sidecar files.
- Keep article media near the article or under a stable site folder.
- Prefer descriptive filenames for covers and diagrams.
- Update the matching `site/*.json` or `site/**/navigation.json` file when adding new navigation-level content.
- Avoid committing temporary exports, desktop files, or local-only helper files.
