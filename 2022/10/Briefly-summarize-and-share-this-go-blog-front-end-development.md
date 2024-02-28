---
title: 简单总结分享这次go博客前台开发
slug: Briefly-summarize-and-share-this-go-blog-front-end-development
description: 有网友让分享，简单说说吧
date: 2022-10-10 09:41:26
copyright: Original
author: 沙漠尽头的狼
draft: false
cover: https://img1.dotnet9.com/2022/10/go-blog-new-cover.png
categories: 更多语言
tags: Go
---

大家好，我是沙漠尽头的狼。

![本文写作由来](https://img1.dotnet9.com/2022/10/blog-post-write-reason.png)

站长用Go写的这版[博客](https://go.dotnet9.com)前台[代码](https://github.com/dotnet9/Dotnet9/tree/develop/src/Dotnet9.Web.Go)比较原生，既没用什么框架，本来想着后面重构后再详细写写分享的，今天群友提了，就简单写写吧。

在线访问地址：[https://go.dotnet9.com](https://go.dotnet9.com)

首页截图：

![](https://img1.dotnet9.com/2022/10/go-blog-new-cover.png)

本文写的很简单，还是来个目录：

1. Go基础学习
2. Go搭建博客参考
3. Go版本博客源码
4. 后面计划

## 1. Go基础学习

- Go中文文档：[https://go.p2hp.com/doc/](https://go.p2hp.com/doc/)
- Go web编程：Q群(771992300)有PDF文件分享

上面的网址和pdf，适合打基础、巩固Go的基础语法。其中PDF文件应该不是京东售卖的《Go Web编程》（[新加坡] 郑兆雄）一书，但也非常不错，讲解了不少web基础知识，应该是网上一位大佬的总结，值得一读，下面是该pdf目录：

![Go web PDF](https://img1.dotnet9.com/2022/10/go-web-pdf.gif)

上面的基础资料建议未接触Go的同学阅读，基础很重要。

## 2. Go搭建博客参考

现重构的博客前台模板和Go版本学习资料出处：

- B站Up主【码神之路】B站首页：[https://space.bilibili.com/473844125](https://space.bilibili.com/473844125)
- 教程标题：【码神之路】原生Go语言博客实战教程，练手级项目实战教程，未使用任何框架，通俗易懂，十年大厂程序员讲解

- 视频链接：[https://www.bilibili.com/video/BV1VS4y1F7NM](https://www.bilibili.com/video/BV1VS4y1F7NM)

视频目录如下：

![码神之路B站视频教程目录](https://img1.dotnet9.com/2022/10/blog-bilibili-catalogue.gif)

- 博文链接：[https://www.mszlu.com/goblog/01.html#_1-%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE](https://www.mszlu.com/goblog/01.html#_1-%E6%90%AD%E5%BB%BA%E9%A1%B9%E7%9B%AE)

视频配套的博文截图：

![码神之路个人博客](https://img1.dotnet9.com/2022/10/go-blog-of-mszlu.png)

视频适合学习、动手实践，Up主配套的博文则适合辅助检查代码是否正确，博客配套的前端模板在Up主群里有分享（群号【718840650】），站长代码仓库也有上传，但是有部分与Up主改动不同，部分个性化修改如下：

- 站长未添加前台文章编辑：Dotnet9网站有后台文章管理功能，功能重合了。
- ORM选择不同：站长选择的GORM。
- 文章搜索站长直接调用的Dotnet9网站后端接口，未在Go中再写接口实现：Web API与前台职责分明，也为了其他客户端接口共用，比如Razor Pages博客前台也使用了相同的文章搜索接口。
- 其他。

## 3. Go版本博客源码

如B站Up主【码神之路】视频教程标题所说“原生Go语言博客实战教程，练手级项目实战教程，未使用任何框架，通俗易懂”，重点是原生，站长实践后发现Up主的路由相关写法与 `ASP.NET Core` 的`Minimal APIs`（最小API）相像，当然前者主要是写Web（MVC），后者是写Web API，实践中与自己熟悉的技术比较学习能加深理解，下面对Go版博客源码进行部分简单介绍。

### 3.1 入口

**main.go**为程序入口文件，相当于 `ASP.NET Core` 的`Program.cs`文件，文件名可以修改，只要代码第一行为`package main`。

全部代码如下：

```go
package main

import (
	"log"
	"net/http"
	"dotnet9.com/goweb/common"
	"dotnet9.com/goweb/router"
)

func init() {
	// 模板加载：html文件，比如首页文章列表、分类文章列表、页头、页尾等
	common.LoadTemplate()
}

func main() {
	server := http.Server{
		Addr: "127.0.0.1:8080",
	}
	router.Router()
	if err := server.ListenAndServe(); err != nil {
		log.Fatal(err)
	}
}
```

服务绑定IP与端口是写死的，可写到配置文件，你就说和如下 `ASP.NET Core` 最小API像不像吧：

```csharp
var app = WebApplication.Create(args);

app.MapGet("/", () => "Hello World!");

app.Run("http://localhost:3000");
```

`router.Router()`是简单封装的路由(封装如下），和上面最小API`app.MapGet("/", () => "Hello World!");`像几分，能给到8分不？：

go的简单路由封装：**router.go**

```go
package router

import (
	"net/http"
	"dotnet9.com/goweb/views"
	"dotnet9.com/goweb/api"
)

func Router() {
	http.HandleFunc("/", views.HTML.Index)
	http.HandleFunc("/c/", views.HTML.Category)
	http.HandleFunc("/p/", views.HTML.Detail)
	http.HandleFunc("/login", views.HTML.Login)
	http.HandleFunc("/api/v1/post", api.API.SaveAndUpdatePost)
	http.Handle("/resource/", http.StripPrefix("/resource/", http.FileServer(http.Dir("public/resource/"))))
}
```

路由首页代码如下：

**./views/index.go** 

```go
package views

import (
	"errors"
	"log"
	"net/http"
	"strconv"
	"dotnet9.com/goweb/common"
	"dotnet9.com/goweb/service"
)

func (*HTMLApi) Index(w http.ResponseWriter, r *http.Request) {
	// 这里是业务代码，读取文章列表，绑定模板
}
```

### 3.n 模板绑定

中间跳过orm等使用，说说模板绑定：

这是Go的文章列表模板：**./template/layout/post-list.html**

```html
{{define "post-list"}}
<ul class="post-box">
  {{range $index, $elem := .Posts}}
  <li class="post-label">
    <a href="/p/{{$elem.Slug}}"><h2>{{$elem.Title}}</h2></a>
    <p>{{$elem.Description}}</p>
    <div class="post-action">
      <span>
        <i class="iconfont icon-yonghu"></i>
        &nbsp; {{$elem.Original}}
      </span>
      <span>
        <i class="iconfont icon-wenjianjia"></i>
        {{range $catIndex, $cat := $elem.Categories}}
        &nbsp;<a href="/c/{{$cat.Slug}}">{{$cat.Name}}</a>
        {{end}}
      </span>
      <span>
        <i class="iconfont icon-riqi"></i>
        &nbsp;{{$elem.CreationTime}}
      </span>
      <span>
        <i class="iconfont icon-yanjing"></i>
        &nbsp;{{$elem.ViewCount}}
      </span>
    </div>
  </li>
  {{end}}
</ul>
{{end}}
```

对比这是`Razor Pages`文章列表模板绑定：

```html
@page
@inject IOptionsSnapshot<SiteOptions> SiteOptions
@using Dotnet9.Web.ViewModel.Commons
@model IndexModel
@{
    ViewData["Title"] = "首页";
}

<ul class="post-box">
    @if (Model.BlogPosts?.Any() == false)
    {
        <span>没有数据哦</span>
    }
    else
    {
        @foreach (var blogPost in Model.BlogPosts!)
        {
            <li class="post-label">
                <a href="@blogPost.CreationTime.ToString("/yyyy/dd")/@blogPost.Slug">
                    <h2>@blogPost.Title</h2>
                </a>
                <p>@blogPost.Description</p>
                <div class="post-action">
                    <span>
                        <i class="iconfont icon-yonghu"></i>
                        &nbsp; @(blogPost.Original.IsNullOrWhiteSpace() ? SiteOptions.Value.Owner : blogPost.Original)
                    </span>
                    <span>
                        <i class="iconfont icon-wenjianjia"></i>
                        &nbsp;
                        @foreach (var cat in blogPost.Categories)
                        {
                            <a href="/cat/@cat.Slug" title="@cat.Description">@cat.Name</a>
                        }
                    </span>
                    <span>
                        <i class="iconfont icon-riqi"></i>
                        &nbsp;@blogPost.CreationTime.ToString("yyyy-MM-dd")
                    </span>
                    <span>
                        <i class="iconfont icon-yanjing"></i>
                        &nbsp;@blogPost.ViewCount
                    </span>
                </div>
            </li>
        }
    }
</ul>

@await Html.PartialAsync("_PaginationPartial", new PaginationModel(Model.Current, Model.Pages, Model.Total, Model.PageSize, Model.PageCount))
```

代码看个大概：

- Go模板绑定使用`{{.对象字段}}`，注意前面的点
- Razor语法绑定使用`@Model.对象`

语言都是相似的，熟悉一个技术栈，其他技术栈可以触类旁通。

### 3.n+1评论

评论对接的第三接口`Valine`，后面考虑自己开发：

>Valine - 一款快速、简洁且高效的无后端评论系统。

**特性**
- 快速
- 安全
- Emoji 😉
- 无后端实现
- MarkDown 全语法支持
- 轻量易用
- 文章阅读量统计 v1.2.0+

网站使用截图：

![](https://img1.dotnet9.com/2022/10/blog-comment-valine.png)

Valine留言后台管理：

![](https://img1.dotnet9.com/2022/10/admin-of-valine.png)

## 4. 后面计划

学习Go，或者一门新技术，不能光是学，需要找机会用起来。尝试使用Go做一个自己感兴趣的项目，比如开发一个简单的博客前台，相信自己会学习的更有动力，起初可以尝试原生开发，即不使用框架，对了解一门语言的基础语法很有益处。

当想快速进行业务开发、迭代时，可以考虑框架了，站长最近有关注 [goframe](https://goframe.org/pages/viewpage.action?pageId=1114119)：

>GoFrame是一款模块化、高性能、企业级的Go基础开发框架。GoFrame是一款通用性的基础开发框架，是Golang标准库的一个增强扩展级，包含通用核心的基础开发组件，优点是实战化、模块化、文档全面、模块丰富、易用性高、通用性强、面向团队。如果您想使用Golang开发一个业务型项目，无论是小型还是中大型项目，GoFrame是您的不二之选。如果您想开发一个Golang组件库，GoFrame提供开箱即用、丰富强大的基础组件库也能助您的工作事半功倍。如果您是团队Leader，GoFrame丰富的资料文档、详尽的代码注释、活跃的社区成员将会极大降低您的指导成本，支持团队快速接入、语言转型与能力提升。

GoFrame官方文档：https://goframe.org/pages/viewpage.action?pageId=1114119

![](https://img1.dotnet9.com/2022/10/goframe-cover.png)

后面就用goframe重构这版go博客吧，当然前提是先把`Razor Pages`版本博客开发完善了，包括后台...

水文结束（花了2小时），只要肯花时间学，没什么学不会的，不服就一个字：整。