---
title: AI重构Razor Pages网站完成
slug: website-reconstruction-with-ai-razor-pages
description: 从Blazor静态SSR回归Razor Pages，源码解读网站架构设计与核心实现
date: 2026-04-16 22:45:00
lastmod: 2026-04-16 23:00:00
cover: https://img1.dotnet9.com/2026/04/cover_01.svg
banner: false
categories:
  - .NET
  - Web开发
  - AI辅助开发
tags:
  - Razor Pages
  - ASP.NET Core
  - AI辅助开发
---

![](https://img1.dotnet9.com/2026/04/cover_01.svg)

网站又从Blazor回归Razor Pages，本文从源码角度解读网站当前的架构设计。

## 1. 为什么从Blazor回归Razor Pages

网站前台之前使用 Blazor 静态 SSR 开发，随着对内容展示类网站的深入思考，认为 Razor Pages 可能更适合这类场景。

## 2. 网站项目结构

源码仓库：[CodeWF](https://github.com/dotnet9/CodeWF)，采用前后台分离架构：

```
CodeWF/src/
├── WebApp/                 # 前台站点（Razor Pages）
│   ├── Pages/             # 页面文件
│   ├── Components/        # View Components
│   ├── Controllers/      # API控制器
│   └── wwwroot/          # 静态资源
│
└── CodeWF/                # 核心类库
    ├── Models/           # 数据模型
    ├── Services/         # 业务服务
    └── Extensions/       # 扩展方法
```

![CodeWF架构图](https://img1.dotnet9.com/2026/04/codewf-architecture.svg)

## 3. Razor Pages核心实现

### 3.1 页面模型（PageModel）

以工具页面为例，展示典型的PageModel写法：

```csharp
// Pages/Tool/Index.cshtml.cs
namespace WebApp.Pages.Tool;

public class IndexModel : PageModel
{
    private readonly AppService _appService;

    public List<ToolItem>? Tools { get; set; }

    public IndexModel(AppService appService)
    {
        _appService = appService;
    }

    public async Task OnGetAsync()
    {
        Tools = await _appService.GetAllToolItemsAsync();
    }
}
```

对应视图文件 [Pages/Tool/Index.cshtml](https://github.com/dotnet9/CodeWF/blob/main/src/WebApp/Pages/Tool/Index.cshtml)：

```cshtml
@page
@model WebApp.Pages.Tool.IndexModel
@{
    ViewData["Title"] = "工具";
}

<div class="container">
    <h1 class="mb-4">在线工具</h1>
    <div class="row">
        @foreach (var tool in Model.Tools)
        {
            <div class="col-md-4 mb-4">
                <div class="card shadow-sm h-100">
                    <div class="card-body">
                        <h4 class="card-title">@tool.Name</h4>
                        @if (!string.IsNullOrEmpty(tool.Memo))
                        {
                            <p class="card-text text-muted">@tool.Memo</p>
                        }
                        @if (tool.Children != null && tool.Children.Any())
                        {
                            <ul class="list-group list-group-flush mt-3">
                                @foreach (var child in tool.Children)
                                {
                                    <li class="list-group-item">
                                        <a href="@child.Slug">@child.Name</a>
                                    </li>
                                }
                            </ul>
                        }
                    </div>
                </div>
            </div>
        }
    </div>
</div>
```

### 3.2 路由参数绑定

文章详情页使用路由模板语法：

```cshtml
@page "/{year:int}/{month:int}/{slug}"
@model WebApp.Pages.Bbs.Post.IndexModel
```

对应代码后端 [Post/Index.cshtml.cs](https://github.com/dotnet9/CodeWF/blob/main/src/WebApp/Pages/Bbs/Post/Index.cshtml.cs)：

```csharp
public class IndexModel : PageModel
{
    public BlogPost? Post { get; set; }

    public async Task OnGetAsync(int year, int month, string slug)
    {
        Post = await _appService.GetPostBySlug(slug);
    }
}
```

### 3.3 共享布局（Layout）

Layout文件定义全局页面结构：

```cshtml
@inject CodeWF.Services.AppService AppService
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>@ViewData["Title"] - 码坊</title>
    <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.min.css" />
    <link rel="stylesheet" href="~/css/site.css" asp-append-version="true" />
</head>
<body>
    <header>
        <nav class="navbar navbar-expand-lg navbar-dark bg-tech-nav fixed-top">
            <!-- 导航栏内容 -->
        </nav>
    </header>

    <div style="padding-top: 80px;">
        @RenderBody()
    </div>

    @await Component.InvokeAsync("FriendLink")

    <footer>
        <!-- 页脚内容 -->
    </footer>
</body>
</html>
```

## 4. 服务层设计

### 4.1 AppService核心服务

[AppService.cs](https://github.com/dotnet9/CodeWF/blob/main/src/WebApp/Services/AppService.cs) 是整个应用的核心服务类：

```csharp
public class AppService(IOptions<SiteOption> siteOption)
{
    private List<BlogPost>? _blogPosts;
    private List<ToolItem>? _toolItems;
    private List<DocItem>? _docItems;
    private List<CategoryItem>? _categoryItems;
    private List<AlbumItem>? _albumItems;

    public async Task<List<BlogPost>?> GetAllBlogPostsAsync()
    {
        if (_blogPosts?.Any() == true) return _blogPosts;

        _blogPosts = [];
        var endYear = DateTime.Now.Year;

        for (var start = siteOption.Value.StartYear; start <= endYear; start++)
        {
            var postDir = Path.Combine(siteOption.Value.LocalAssetsDir, start.ToString());
            if (!Directory.Exists(postDir)) continue;

            var postFiles = Directory.GetFiles(postDir, "*.md", SearchOption.AllDirectories);
            foreach (var postFile in postFiles)
            {
                var blogPost = await ReadBlogPostAsync(postFile);
                if (!blogPost.Draft)
                {
                    _blogPosts.Add(blogPost);
                }
            }
        }

        _blogPosts = _blogPosts
            .OrderByDescending(post => post.Lastmod ?? post.Date ?? DateTime.MinValue)
            .ThenByDescending(post => post.Date ?? DateTime.MinValue)
            .ToList();
        return _blogPosts;
    }

    public async Task SeedAsync()
    {
        // 启动时预加载所有数据
        await GetAllAlbumItemsAsync();
        await GetAllCategoryItemsAsync();
        await GetAllBlogPostsAsync();
        await GetAllFriendLinkItemsAsync();
        await GetAllDocItemsAsync();
        await GetAllToolItemsAsync();
    }
}
```

### 4.2 数据模型

```csharp
// BlogPost.cs
public class BlogPostBrief
{
    public string? Title { get; set; }
    public string? Slug { get; set; }
    public string? Description { get; set; }
    public DateTime? Date { get; set; }
    public DateTime? Lastmod { get; set; }
    public string? Author { get; set; }
    public string? Cover { get; set; }
    public List<string>? Categories { get; set; }
    public List<string>? Tags { get; set; }
}

public class BlogPost : BlogPostBrief
{
    public string? Content { get; set; }      // 原始Markdown
    public string? HtmlContent { get; set; }  // 转换后的HTML
}
```

## 5. 页面请求处理流程

![页面请求处理流程](https://img1.dotnet9.com/2026/04/razor-pages-request-flow.svg)

1. 用户请求 `/timestamp`
2. 路由系统匹配到 `@page "/timestamp"`
3. 执行 `OnGetAsync()` 方法（若存在）
4. 通过 `AppService` 获取业务数据
5. 渲染视图并返回HTML

## 6. Program.cs配置

```csharp
var builder = WebApplication.CreateBuilder(args);

// 添加Razor Pages服务
builder.Services.AddRazorPages();
builder.Services.AddControllers();
builder.Services.AddHttpClient();

// 依赖注入AppService
builder.Services.AddSingleton<AppService>();
builder.Services.Configure<SiteOption>(builder.Configuration.GetSection("Site"));

var app = builder.Build();

// 启动时预加载数据
using (var serviceScope = app.Services.CreateScope())
{
    var service = serviceScope.ServiceProvider.GetRequiredService<AppService>();
    await service.SeedAsync();
}

app.UseHttpsRedirection();
app.UseStaticFiles();
app.UseRouting();
app.UseAuthorization();

app.MapRazorPages();
app.MapControllers();

app.Run();
```

## 7. 在线工具示例：时间戳转换

以 [Timestamp.cshtml](https://github.com/dotnet9/CodeWF/blob/main/src/WebApp/Pages/Tool/Converter/Timestamp.cshtml) 为例展示前端交互：

```cshtml
@page "/timestamp"
@{ ViewData["Title"] = "时间戳转换工具"; }

<div class="container">
    <h1 class="mb-4">时间戳转换工具</h1>

    <div class="card mb-3">
        <div class="card-body">
            <h5 class="card-title">时间戳 → 日期</h5>
            <div class="row mb-3">
                <div class="col d-flex align-items-center gap-2">
                    <span>时间戳:</span>
                    <input type="text" class="form-control" style="width: 200px;"
                           id="inputTimestamp" placeholder="输入时间戳" />
                    <select class="form-select" style="width: 100px;" id="timestampUnit">
                        <option value="s">秒</option>
                        <option value="ms">毫秒</option>
                    </select>
                    <button class="btn btn-primary btn-sm"
                            onclick="convertTimestampToDate()">转换为日期</button>
                </div>
            </div>
        </div>
    </div>
</div>

@section Scripts {
    <script>
        function convertTimestampToDate() {
            const timestamp = document.getElementById('inputTimestamp').value;
            const unit = document.getElementById('timestampUnit').value;
            const ms = unit === 's' ? timestamp * 1000 : parseInt(timestamp);
            const date = new Date(ms);
            document.getElementById('outputDate').value = date.toLocaleString();
        }
    </script>
}
```

## 8. 总结

源码已开源，欢迎交流学习。

**仓库地址**：https://github.com/dotnet9/CodeWF
