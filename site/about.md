# 关于本站及站长

<p align="center">
  <a href="https://dotnet9.com">
    <img src="https://img1.dotnet9.com/site/logo.png" width="128" height="128" alt="Dotnet9">
  </a>
</p>

<h1 align="center">Dotnet9</h1>

<div align="center">

一个使用`Dotnet 8`开发的`博客`系统，一直在开发中...

 ![dotnet-version](https://img.shields.io/badge/.NET%207.0-blue)  ![Visual Studio 2022](https://img.shields.io/badge/Visual%20Studio%20-2022-blueviolet)  <a target="_blank" href="https://qm.qq.com/cgi-bin/qm/qr?k=iL6egdGSGCMPezcUyzMPEcs9qsllgwr-&jump_from=webapi"><img border="0" src="https://pub.idqqimg.com/wpa/images/group.png" alt="Dotnet9软件技术交流" title="Dotnet9软件技术交流"></a> [![码云](https://img.shields.io/badge/Gitee-%E7%A0%81%E4%BA%91-orange)](https://gitee.com/dotnet9/Dotnet9)   [![Github](https://img.shields.io/badge/%20-github-%2324292e)](https://github.com/dotnet9/Dotnet9) [![Github stars](https://img.shields.io/github/stars/dotnet9/Dotnet9)](https://github.com/dotnet9/Dotnet9)

 </div>

 ## 0. 最新开发情况

- [x] 前台
  1. [x] 使用 [ASP.NET Core 8 Razor Pages](https://learn.microsoft.com/zh-cn/aspnet/core/razor-pages/?view=aspnetcore-8.0&tabs=visual-studio) 搭建前台。
  2. [x] 已有功能：文章列表、分类文章列表、专辑文章列表、文章归档、时间轴、网站地址、标签云、文章详情、RSS等。
  3. [ ] 还有很多功能待开发，比如登录、前台文章创建和文章修改等。
- [x] 后台前端
  1. [x] 使用[React](https://react.dev/)搭建后台前端。
  2. [x] 组件库使用[Semi-UI](https://semi.design/zh-CN/start/getting-started)。
  3. [ ] 参考的开源项目[TokenBlog](https://github.com/239573049/TokenBlog)后台[前端](https://github.com/239573049/TokenBlog/tree/master/web)，正在调试中。
- [x] 后端
  1. [x] 使用 [ASP.NET Core 8.0 Web API](https://learn.microsoft.com/zh-cn/aspnet/core/web-api/?view=aspnetcore-8.0) 搭建，框架选择 [Masa Framework(DDD+CQRS)](https://www.masastack.com/framework)。
  2. [x] 数据库使用 [EF Core](https://learn.microsoft.com/zh-cn/ef/core/) + [PostgreSQL](https://www.postgresql.org/)
  3. [ ] 根据前台和后台前端的功能迭代，进行维护中。

## 1. Python之禅

介绍本人及本站之前，我们看看 Python 之禅，在 Python Shell 中输入 import this 命令，显示内容就是 Python 之禅，见下文：

```shell
The Zen of Python, by Tim Peters
Beautiful is better than ugly.
Explicit is better than implicit.
Simple is better than complex.
Complex is better than complicated.
Flat is better than nested.
Sparse is better than dense.
Readability counts.
Special cases aren’t special enough to break the rules.
Although practicality beats purity.
Errors should never pass silently.
Unless explicitly silenced.
In the face of ambiguity, refuse the temptation to guess.
There should be one– and preferably only one –obvious way to do it.
Although that way may not be obvious at first unless you’re Dutch.
Now is better than never.
Although never is often better than right now.
If the implementation is hard to explain, it’s a bad idea.
If the implementation is easy to explain, it may be a good idea.
Namespaces are one honking great idea — let’s do more of those!
```

```shell
Python 之禅 by Tim Peters
优美胜于丑陋。
明了胜于晦涩。
简洁胜于复杂。
复杂胜于凌乱。
扁平胜于嵌套。
宽松胜于紧凑。
可读性很重要。
即便是特例，也不可违背这些规则。
不要捕获所有错误，除非你确定需要这样做。
如果存在多种可能，不要猜测。
通常只有唯一一种是最佳的解决方案。
虽然这并不容易，因为你不是 Python 之父。
做比不做要好，但不假思索就动手还不如不做。
如果你的方案很难懂，那肯定不是一个好方案，反之亦然。
命名空间非常有用，应当多加利用。
```

## 2. 关于本站

站长平时忙于编程和带娃，建此站目的如下：

1. 对编程技术进行一些积累总结；
2. 记录带娃的点点心酸感悟。

## 3. 站长简介

你好，我是本站（https://dotnet9.com）站长“沙漠尽头的狼”。

本人从事Dotnet开发10年+，建此站目的在于分享以Dotnet为主的技术类文章，希望以此平台与更多的程序员朋友交流技术，祝愿Dotnet社区发展越来越好。 

89年Dotnet程序猿一枚，C#高级工程师， 目前从事B/S开发工作。

## 4. 站长技术栈

1. 开发语言：C/S（C# + WPF\Winform，C++ + Qt Widgets, SwiftUI)、B/S(ASP.NET Core MVC，ASP.NET Core Blazor，React + ASP.NET Core Web API)。
2. 开发框架：C/S(Prism、Qt Plugin System)、B/S(前端 Ant Design Pro，后端 ASP.NET Core Web API)等。
3. C/S 控 件 库：Dev Express、Telerik、部分开源控件库(MaterialDesignInXamlToolkit、MahApps.Metro、HandyControl)等。
4. B/S控件库：Ant Design、Bootstrap、Masa Blazor等。
5. 通信协议：SignalR、Remoting、WebService、TCP/IP、UDP、HTTP、FTP、ICE、DDS、KNET、ENET、软件总线、事件总线等。
6. 开发工具：Visual Studio、Qt Creator、Visual Studio Code、xCode等。
7. 数 据 库：MySQL、Oracle、SQLServer、SQLite、Access、Redis等。
8. 影音办公类：Adobe（Photoshop 、Axure）、OFFICE（Visio、Word、PPT、Excel）等应用软件。

## 5. 联系站长

技术交流群

1. 添加站长个人微信(dotnet9com)加入微信群。

![Dotnet9](https://img1.dotnet9.com/site/wechatowner.jpg)

2. QQ群：771992300

![Dotnet9技术交流QQ群](https://img1.dotnet9.com/site/qqgoup1.png)

群规：

新人入群备注个人群昵称：[地区]-[昵称]，如群主：成都-沙漠尽头的狼。

本群讨论话题不止dotnet技术，各技术之间取长补短，相互学习。

0. 打造高质量群，从自己做起。
1. 不参与可能无脑对立类话题，比如语言优劣性和转语言类型话题。
2. 讨论技术请带上场景，解决技术是思路，而不是数学题，并不存在唯一答案，禁止强烈灌输思想。
3. 邀请群成员加入，请主动艾特管理员，并说明原因，原则上非技术人员，不予批准。
4. 不闲聊，不搭讪，不吹水。做一个安静的群。只分享技术。本群并不打算让大家把吹水群从QQ群迁移到微信群，这样的吹水毫无意义。
5. 这个群是纯粹的技术交流群，期望大家更多的分享自己遇到的技术问题。请不要吹水，要做到言之有物。 
6. 本群的目的是为实现技能的长期沉淀！
7. 为了保护群主和其他管理员的安全。请在国家法律允许范围内聊天。违禁词包括色情、政治，宗教信仰，地方歧视，语言歧视，棋牌，和其他一切与现行法律法规不符的地方。

## 6. 站长其他平台链接：

1. QQ邮箱：[632871194@qq.com](https://mail.qq.com/cgi-bin/qm_share?t=qm_mailme&email=632871194@qq.com)
2. YouTube：[https://www.youtube.com/channel/UCYVJHthYAcGQgOHNFQC0ylQ](https://www.youtube.com/channel/UCYVJHthYAcGQgOHNFQC0ylQ)
3. B站：[https://space.bilibili.com/470546606](https://space.bilibili.com/470546606)
4. 博客园： [https://www.cnblogs.com/Dotnet9-com/](https://www.cnblogs.com/Dotnet9-com/)
5. 今日头条： [https://www.toutiao.com/c/user/98075192460/#mid=1651788205627396](https://www.toutiao.com/c/user/98075192460/#mid=1651788205627396)
6. 简书：[https://www.jianshu.com/u/c272a0a47d13](https://www.jianshu.com/u/c272a0a47d13)
7. 知乎：[https://www.zhihu.com/people/dotnet9/activities](https://www.zhihu.com/people/dotnet9/activities)
8. 开源中国：[https://my.oschina.net/dotnet9](https://my.oschina.net/dotnet9)
9. CSDN：[https://me.csdn.net/HenryMoore](https://me.csdn.net/HenryMoore)
10. 掘金：[https://juejin.im/user/1732486058751934](https://juejin.im/user/1732486058751934)
11. Telegram：[https://t.me/dotnet9](https://t.me/dotnet9)
12. Github：[https://www.github.com/dotnet9](https://www.github.com/dotnet9)
13. Gitee：[https://gitee.com/dotnet9](https://gitee.com/dotnet9)
14. 51CTO博客：[https://blog.51cto.com/u_15469207](https://blog.51cto.com/u_15469207)
15. 51Aspx：[https://club.51aspx.com/users/czwzxCmkj/post](https://club.51aspx.com/users/czwzxCmkj/post)
16. InfoQ：[https://www.infoq.cn/profile/A417E2BA797068/publish](https://www.infoq.cn/profile/A417E2BA797068/publish)
17. 腾讯云开发者社区：[https://cloud.tencent.com/developer/user/4880446](https://cloud.tencent.com/developer/user/4880446)
18. 阿里云开发者社区：[https://developer.aliyun.com/profile/xzbu4dxrupikc](https://developer.aliyun.com/profile/xzbu4dxrupikc)
19. 华为云开发者社区：[https://bbs.huaweicloud.com/community/usersnew/id_1676989516009343](https://bbs.huaweicloud.com/community/usersnew/id_1676989516009343)