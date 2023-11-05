---
title: 蚂蚁集团旗下语雀崩溃！如何避免类似情况？打造个性化博客网站，稳定无惧！
slug: ant-groups-language-sparrow-collapses-how-to-avoid-similar-situations-create-a-personalized-blog-website-stable-and-fearless
description: 昨天（2023年10月23日）蚂蚁集团旗下语雀崩了：在线文档及官网均无法打开 官方称紧急恢复中，建议读者自己开发一个属于自己的博客网站，遇到这种事才不会惊慌。
date: 2023-10-24 09:31:25
lastmod: 2023-11-05 12:15:18
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2023/10/cover_03.png
categories: 科技生活
---

## 1. 本文背景

昨天（2023年10月23日）有两个新闻在站长小圈子里讨论的比较火热：

1. 第一个是娱乐新闻【章子怡和汪峰离婚】，这里我们不聊；
2. 第二个是蚂蚁集团旗下的在线文档平台语雀突然崩溃了，无论是在线文档还是官方网站都无法打开，官方紧急发布公告称正在紧急恢复中。

**头条：章子怡和汪峰离婚**

![章子怡和汪峰离婚](https://img1.dotnet9.com/2023/10/0308.jpg)

**次条：蚂蚁集团旗下的在线文档平台语雀突然崩溃**

![蚂蚁集团旗下的在线文档平台语雀突然崩溃](https://img1.dotnet9.com/2023/10/0301.png)

下面这张图来自知乎：https://www.zhihu.com/question/627397548

![](https://img1.dotnet9.com/2023/10/0309.png)

**昨晚语雀已经恢复正常**

![昨晚语雀已经恢复正常](https://img1.dotnet9.com/2023/10/0310.png)

面对语雀突然崩溃这一突发情况，站长建议读者们自己开发一个属于自己的博客网站，以免在类似事件发生时陷入惊慌。

## 2. 程序员应该开发自己的博客网站

这是站长自己的网站[Dotnet9](https://dotnet9.com/):

![Dotnet9网站首页](https://img1.dotnet9.com/2023/10/0302.png)

目前只做了前台展示([Razor Pages](https://learn.microsoft.com/zh-cn/aspnet/core/razor-pages/?view=aspnetcore-8.0&tabs=visual-studio))和后端[Web API](https://learn.microsoft.com/zh-cn/aspnet/core/web-api/?view=aspnetcore-8.0)接口，一直在持续优化中，打算参考一个成熟的博客网站再次重构。

## 3. Dotnet9重构计划

大家可以看看这个博客网站 [可乐不加冰](https://www.okay123.top/)，下面是该博客网站效果图，[可乐不加冰源码](https://gitee.com/miss_you/easy-admin)在这，站长打算参考这个网站重构。

### 3.1. 参考网站前台效果图

**首页**

![首页](https://img1.dotnet9.com/2023/10/0303.png)

**首页文章列表**

![首页文章列表](https://img1.dotnet9.com/2023/10/0304.png)

**文章分类**

![文章分类](https://img1.dotnet9.com/2023/10/0314.png)

**文章详情**

![文章详情](https://img1.dotnet9.com/2023/10/0305.png)

**文章评论**

![文章评论](https://img1.dotnet9.com/2023/10/0306.png)

**留言**

![留言](https://img1.dotnet9.com/2023/10/0307.png)

### 3.2. 参考网站后台效果图

![登录](https://img1.dotnet9.com/2023/10/0311.png)

![面板](https://img1.dotnet9.com/2023/10/0312.png)

![文章列表](https://img1.dotnet9.com/2023/10/0313.png)

### 3.3. 站长重构想法

站长一直想使用.NET Core + Vue开发博客网站系统，奈何Vue技术储备不足，正好这个开源项目功能比较齐全，界面也比较炫酷，非常适合参考做站，那就研究下吧，该网站源码就不描述了，后面重构完再写篇文章分享。

## 4. 总结（Dotnet9网站愿景）

秉持着互联网分享精神，让用户能够自给自足，完全自己掌控自己的博客内容和网站运营。这个系统的开发使用.NET Core作为后端框架，Vue作为前端框架，结合了两者的优势，为用户提供了一个稳定、高效的博客网站搭建方案。

使用.NET Core作为后端框架的好处在于它是一个跨平台的开发框架，可以在Windows、Linux和macOS等多个操作系统上运行。这意味着用户可以选择自己喜欢的操作系统来搭建和运行自己的博客网站，不再受限于特定的操作系统。同时，.NET Core还具有高性能和可扩展性的特点，可以处理大量的并发请求，确保用户在访问博客网站时能够获得流畅的体验。

而Vue作为前端框架则提供了丰富的组件和工具，使得用户可以轻松地构建出美观、交互丰富的博客网站界面。Vue的响应式设计和虚拟DOM技术，使得页面的更新和渲染更加高效，用户可以快速地浏览和阅读博客内容。

通过使用.NET Core + Vue的博客网站系统，用户可以完全自主地管理自己的博客内容和网站运营。用户可以自由地发布、编辑和删除博客文章，上传和管理图片和文件等。同时，用户还可以通过系统提供的评论功能与读者进行互动，分享自己的见解和经验。

此外，由于博客网站系统完全由用户自己掌控，用户不再依赖于第三方平台，不会受到类似语雀崩溃的事件的影响。用户可以随时备份和恢复自己的博客数据，确保数据的安全性和可靠性。同时，用户还可以根据自己的需求和喜好，自由地扩展和定制博客网站的功能和特性。

总之，面对语雀崩溃的突发情况，站长建议读者们自己开发一个属于自己的博客网站，以免在类似事件发生时陷入惊慌。使用.NET Core + Vue的博客网站系统，用户可以自给自足，完全自己掌控自己的博客内容和网站运营，享受互联网分享的乐趣。

**资料**

- 站长欲参考做站的可乐不加冰博客网站：https://www.okay123.top/
- 后端框架Furion文档站：http://furion.baiqian.ltd/
- ORM框架SqlSugar果糖网：https://www.donet5.com/

站长最后提一句，也是常在技术群里说的：.NET使用的人本来就不多，没必要因为别人使用了哪个框架就在那里diss，有问题就解决问题，不要说太多没用的话。

## 5. 通知：Dotnet9重构完成

今天（2023年11月5号），站长基本完成了Dotnet9网站的重构工作：

- [x] 博客网站前台
  1. [x] 使用Vue 3.x搭建前台
  2. [x] 已有功能：文章列表、分类文章列表、文章详情、文章评论、归档等
  3. [ ] 其他待补充
- [x] 博客网站后台前端
  1. [x] 使用Vu3 3.x搭建
  2. [x] 基础表的CRUD
  3. [x] 文章管理
  4. [x] 可使用腾讯cos对象存储图片资源，或本地文件系统存储
  5. [ ] 其他功能待补充
- [x] 博客网站后端
  1. [x] 使用ASP.NET Core 8 Web API + Furion + SqlSugar + PostgreSQL搭建
  2. [x] 基础表的接口管理
  3. [ ] 根据前台和后台前端的功能迭代，进行维护中

- [x] Dotnet工具箱
  1. [x] 使用 Vue 3.0 搭建。

成果展示如下：

文章列表：

![Dotnet9文章列表](https://img1.dotnet9.com/2023/10/0315.png)

文章详情页：

![Dotnet9文章详情页](https://img1.dotnet9.com/2023/10/0316.png)

文章章节导航目录：

![Dotnet9文章章节导航目录](https://img1.dotnet9.com/2023/10/0318.png)

文章留言：

![Dotnet9文章留言](https://img1.dotnet9.com/2023/10/0317.png)

依然是原来的配方，原来的文章URL访问格式不变，文章仍然采用`[https://dotnet9.com]/[发布年份]/[发布月份]/[别名]`的格式：

```shell
https://dotnet9.com/2023/10/ant-groups-language-sparrow-collapses-how-to-avoid-similar-situations-create-a-personalized-blog-website-stable-and-fearless
```

- 专辑链接：https://dotnet9.com/albums
- 分类链接：https://dotnet9.com/cats
- 标签：https://dotnet9.com/tags

重构相关资源：

- 参考开源项目easy-admin：https://gitee.com/miss_you/easy-admin
- Dotnet9开源地址：https://github.com/dotnet9/Dotnet9/

站长会持续更新网站源码及文章内容，欢迎大家关注：https://dotnet9.com

- 微信技术交流群：dotnet9com（加站长拉群聊）
- QQ技术交流群：771992300