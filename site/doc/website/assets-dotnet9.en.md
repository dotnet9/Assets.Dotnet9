# Assets.Dotnet9

![Assets.Dotnet9](https://img1.dotnet9.com/site/doc/website/imgs/assets-dotnet9.svg)

`Assets.Dotnet9` is the resource and content repository for the CodeWF website. It is not just an image directory but serves as a file-based content store: articles, project documents, tool metadata, timelines, friend links, site Markdown pages, and accompanying images are all maintained here.

## Who should care

- Those who want to understand how `dotnet9.com` separates content data from the web application source code.
- Those who want to manage Markdown and image assets organized by year, month, category, and project directory.
- Those who want to add new articles, project descriptions, tool entries, or site configurations to the CodeWF website.

## Directory structure

```text
2019/ ... 2026/      Articles and images organized by year and month
site/
  about.md           About page content
  Privacy.md         Privacy page content
  albums.json        Album metadata
  categories.json    Category metadata
  friend-links.json  Friend link metadata
  timelines.json     Timeline data
  doc/               Project center navigation, content, and SVG covers
  tools/             Online tool metadata and preview images
  pays/              Donation page resources
  banners/           Page banner resources
  favicon/           Site icon resources
```

## Content maintenance principles

| Principle | Description |
| --- | --- |
| Stable paths | Article, image, documentation, and tool slugs should remain unchanged as much as possible to avoid breaking historical links. |
| Complete metadata | Article Front Matter, categories, albums, project navigation, and tool JSON need to be maintained synchronously. |
| Clear naming | Use semantic filenames for images, SVGs, and Markdown to facilitate search and reuse. |
| Reviewable | Content changes are primarily text files, suitable for Git diff and Pull Request review. |

## Relationship with CodeWF

`CodeWF` handles the website program, while `Assets.Dotnet9` handles content and resources. When the application starts, it reads `Site:LocalAssetsDir` and loads the content from this repository into the project center, blog, tools, search, and sitemap.

During development, it is recommended to clone both repositories side by side:

```text
CodeWF/
Assets.Dotnet9/
```

## Repositories

- Asset repository: [https://github.com/dotnet9/Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9)
- Website source code: [https://github.com/dotnet9/CodeWF](https://github.com/dotnet9/CodeWF)