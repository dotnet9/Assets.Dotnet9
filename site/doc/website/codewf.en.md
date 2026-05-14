# CodeWF

![CodeWF](https://img1.dotnet9.com/site/doc/website/imgs/codewf.svg)

`CodeWF` is the source code repository for the website `dotnet9.com` / `codewf.com`. The current site is built with ASP.NET Core Razor Pages. It uses the sibling `Assets.Dotnet9` as a file-based content repository, reading articles, project documentation, tool metadata, timelines, friend links, and site-level Markdown pages at runtime.

## Who Should Pay Attention

- You want to understand how `dotnet9.com` organizes Razor Pages, content services, search, and sitemaps.
- You want to separate Markdown, JSON, and image assets from application source code, creating a lightweight CMS-like maintenance workflow.
- You want to reference a .NET-centric site project that includes blogs, project hubs, and online tools.

## Core Responsibilities

| Module | Description |
| --- | --- |
| `src/WebApp/Pages` | Razor Pages including homepage, articles, project center, tools page, search page, etc. |
| `src/WebApp/Services/AppService.cs` | Unified loading of asset repository content, maintaining memory cache, search index, and sitemap. |
| `src/WebApp/Models` | Content models for articles, categories, series, projects, tools, search results, etc. |
| `tests/WebApp.Tests` | Minimal test suite covering Front Matter, Markdown rendering, and search keyword interception. |

## Collaboration with the Asset Repository

The site points to a local `Assets.Dotnet9` directory via `Site:LocalAssetsDir`. In the development environment, file watchers clear the content cache when Markdown, JSON, images, or SVGs change, so you can see results by simply refreshing the page after maintaining the asset repository.

Common inputs include:

- `site/doc/navigation.json`
- `site/tools/tools.json`
- `site/categories.json`
- `site/albums.json`
- `site/timelines.json`
- `site/about.md`
- Article directories from `2019/` to the current year

## Running Locally

```powershell
dotnet run --project src/WebApp/WebApp.csproj
```

If the asset repository is not at the default path, you can override it with an environment variable:

```powershell
$env:Site__LocalAssetsDir = "D:\github\owner\Assets.Dotnet9"
dotnet run --project src/WebApp/WebApp.csproj
```

## Repositories

- Website source code: [https://github.com/dotnet9/CodeWF](https://github.com/dotnet9/CodeWF)
- Content assets: [https://github.com/dotnet9/Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9)