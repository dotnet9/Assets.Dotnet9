---
title: 博客系统知多少：揭秘那些不为人知的学问（四）
slug: How-many-do-you-know-about-the-blog-system-revealing-those-unknown-knowledge-4
description: 大佬说博客
date: 2022-03-08 23:18:36
copyright: Reprinted
author: 汪宇杰博客
originalTitle: 博客系统知多少：揭秘那些不为人知的学问（四）
originalLink: https://mp.weixin.qq.com/s/nDVvAiSYp1EVBMjbwgHajg
draft: False
cover: https://img1.dotnet9.com/2022/03/cover_03.png
categories: 
    - 分享
tags: 
    - 博客
---

上篇[《博客系统知多少：揭秘那些不为人知的学问（三）》](https://mp.weixin.qq.com/s/Z-6on72DM8RNykiTYnY9ug)介绍了博客协议或标准。本篇终章介绍设计博客系统有哪些知识点。

## 目录

由于文章篇幅较长，本文将分为 4 篇推送，目录如下：

1. “博客”的前世今生
2. 我的博客故事
3. 谁是博客的受众？
4. 博客基本功能设计要点
   - 4.1 文章（Post）
   - 4.2 评论（Comment）
   - 4.3 分类（Category）
   - 4.4 标签（Tag）
   - 4.5 归档（Archive）
   - 4.6 页面（Page）
   - 4.7 订阅
   - 4.8 版本控制
   - 4.9 主题及个性化
   - 4.10 用户及权限
   - 4.11 插件
   - 4.12 图片及附件的处理
   - 4.13 脏词过滤及评论审查
   - 4.14 静态化
   - 4.15 通知系统
5. 博客协议或标准
   - 5.1 RSS
   - 5.2 ATOM
   - 5.3 OPML
   - 5.4 APML
   - 5.5 FOAF
   - 5.6 BlogML
   - 5.7 Open Search
   - 5.8 Pingback
   - 5.9 Trackback
   - 5.10 MetaWeblog
   - 5.11 RSD
   - 5.12 阅读器视图
6. 设计博客系统有哪些知识点
   - 6.1 时区真的全用 UTC？
   - 6.2 HTML 还是 Markdown
   - 6.3 MVC 还是 SPA
   - 6.4 安全
7. 结束语

## 6.1 | 时区真的全用 UTC？

存储时间使用 UTC 在 2020 年应该已经是猿尽皆知的实践了，博客系统其实也是如此，我的博客所有时间数据最终保存都采用 UTC 时间。但博客有个特殊的地方，即它不应该按读者的时区去转换 UTC 时间进行显示，而应该按照博客作者的时区去显示时间。

这并不是技术上的原因，就算你按读者时区去显示时间也不会有代码爆炸，原因在于博客的诞生初衷，就是为了彰显个性，让博主在互联网上有自己的展示空间，因此突出博主本人的属性非常重要，博主所在时区也是个让读者了解博主的属性之一，因此，正宗的博客系统都会给一个时区设置选项，并以此转换 UTC 时间作为显示，WordPress 和我的 Moonglade 博客系统均是如此。博客系统不自动转换读者所在时区的时间，纯粹就是个鲜为人知的情怀设计，但必须得尊重。

![(图：Moonglade 按博主设置的时区显示文章发表时间)](https://img1.dotnet9.com/2022/03/0601.png)

那么有意思的事情来了，搜索引擎要怎么理解博客文章的时间？最好将 UTC 时间仅告诉搜索引擎，不要给用户显示，方法也很简单，用 HTML5 的 time 标签的 datetime 属性即可。在 HTML5 标准推广以后，搜索引擎更喜欢看标签类型来判断内容的含义，而不是根据标签里的内容来猜意思。

在 C#里，ToString(“u”)指的是 Universal sortable date/time patter。

```html
<time datetime="@Model.PostModel.PubDateUtc.ToString("u")" title="GMT @Model.PostModel.PubDateUtc">@DateTimeResolver.GetDateTimeWithUserTZone(Model.PostModel.PubDateUtc).ToString("MM/dd/yyyy")</time>
```

对于刚才截图里的文章，时间的 HTML 为：

```html
<time datetime="2020-04-29 11:41:02Z" title="GMT 4/29/2020 11:41:02 AM"
  >04/29/2020</time
>
```

## 6.2 丨 HTML 还是 Markdown

许多技术人士编写博客系统的时候喜欢选用 Markdown 作为编辑器，如果单纯只是个技术博客，自己使用并没有什么问题。但如果你在给他人编写博客系统，请记住，不是每个人，都是程序员，不是每个人，都喜欢 Markdown。

![图 | 网络](https://img1.dotnet9.com/2022/03/0602.gif)

在这种情况下，一个 WSIWYG 的 HTML 编辑器（如 TinyMCE）是不错的选择，HTML 编辑器相对 Markdown 也支持更高级的排版方式。Moonglade 同时支持 HTML 和 Markdown 编辑器。

![（图：Moonglade 使用的TinyMCE编辑器）](https://img1.dotnet9.com/2022/03/0603.png)

保存文章内容到数据库时，Markdown 格式需要选择原始内容，而非生成的 HTML，因为还需要支持后续编辑。HTML 格式现在也不建议 encoding 存储，毕竟都已经 2020 年了，市面上的主流数据库都可以正确支持各种神奇的 Unicode，比如文章中突然出现个 emoji😂，而如果你使用了 encoding，就会像我的博客一样面临一些福报：[https://github.com/EdiWang/Moonglade/issues/280](https://github.com/EdiWang/Moonglade/issues/280)。并且 encoding 和 decoding 的过程会影响性能。我的 Moonglade 博客系统也刚刚完成了去除 encoding 的改造。

## 6.3 丨 MVC 还是 SPA

许多社区里写博客系统的程序员都偏向于使用 SPA 架构建博客，而鄙视用 MVC，觉得落后，真的是这样吗？这个问题就像是飞机为什么不飞直线，是航空公司不会规划吗？关于这一点，我曾经在以前的博客文章《我的 .NET Core 博客性能优化经验总结》中写过：

2014 年以后，随着 SPA 的兴起，Angular 等框架逐渐成为了前端开发的主流。它们解决的问题正是提升前端的响应度，让 Web 应用尽量接近本地原生应用的体验。我也面临过不少朋友的质疑：为什么你的博客不用 angular 写？是你不擅长吗？

![图 | 网络](https://img1.dotnet9.com/2022/03/0604.gif)

其实并不是那么简单。实际上我任职的岗位目前主要工作内容也是写 angular，博客曾经的.NET Framework 版的后台也用过 angularjs 以及 angular2，经过一系列的实践表明，我博客这样的内容站用 angular 收益并不大。

其实这并不奇怪，在盲目选择框架之前，我们得注意一个前提条件：SPA 框架所针对的，其实是 Web 应用。而应用的意思是重交互，即像 Azure Portal 或 Outlook 邮箱那样，目的是把网页当应用程来开发，这时候 SPA 不仅能提升用户体验，也能降低开发成本，何乐而不为？但是博客属于内容为主的网站，不是应用，要说应用也勉强只能说博客的后台管理可以是应用。博客前台唯一的交互就是评论、搜索，因此 SPA 并不适合这样的工作。这就像你要去菜场买菜，骑自行车反而比你开个坦克过去方便。

在微软官方文档里也有同样的关于何时选择 SPA，何时选择传统网站的参考：

[https://docs.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps](https://docs.microsoft.com/en-us/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps)

博客前台仍然选用 MVC 的另一个原因，请回顾一下本文开头“博客的读者是谁”，我运营博客十余年，统计的数据表明，几乎所有的用户都来源于搜索引擎，都只点进来看一篇文章，然后关闭网页。现在仔细想想，SPA 解决的最大的问题之一是什么？是不是通过只刷新局部来提高前端性能（可响应度）？而用户从搜索引擎过来，只看一篇文章就关闭网页，真的用得到 SPA 只刷新局部的优势吗？用户只看一篇文章，你用个 SPA 框架，用户得加载一堆框架本身的文件，其中包括导航、交互等功能，而 99%的用户根本就不会点到别的地方去，于是你只为了 1%的用户，去加载硕大的一个框架，值得吗？这性能到底是提高了，还是降低了？

MVC 框架虽然每次都会输出服务器端渲染的完整 HTML，但由于 99%的用户只看一篇文章就关闭网页，所以对于 99%的用户来说，他们所需要加载的资源，远小于加载一套 SPA，速度更快，还更 SEO 友好。SPA 适合用在博客的后台管理 portal，而不是前台。

## 6.4 丨安全

根据运营博客多年的后台监控数据，最常见的攻击行为是全自动的漏洞扫描工具。他们会请求例如 data.zip, wp-admin.php, git 目录等常见的安全疏忽，或是想要通过某些博客系统的已知漏洞进行攻击。目的是为了控制服务器，在你的博客网页里加入对用户的恶意代码（例如勒索病毒、挖矿）等，有些也会将服务器本身变成矿机。

![（图：Azure后台捕获的自动化扫描工具请求）](https://img1.dotnet9.com/2022/03/0605.png)

设计博客系统时，常用的安全对策可参考 OWASP（[https://owasp.org/](https://owasp.org/)），但同时保留灵活性。例如，加入 JavaScript 的 CSP 时，请考虑正常博客用户可能需要添加三方统计插件（如 Azure Application Insights，国内的 CNZZ 等），请设计一定的黑、白名单或功能开关。

大部分设计者都知道要防范用户的输入，即博客的读者，输入的入口通常只有评论和搜索功能。但不要忘了，博主在博客后台管理中的输入也需要防范，因为不一定是博主本人在操作。举个例子，博主的账号被盗，黑客在后台将导航栏的链接指向黑客的服务器或 localhost 上早已准备好的奇妙的机关（是的，不要以为 localhost 在正常人的电脑上不起作用），那么读者就会受到严重影响。

![图 | 网络](https://img1.dotnet9.com/2022/03/0606.gif)

关于后台登录的身份认证，能采用成熟的 SSO 的就优先采用 SSO，例如 Moonglade 支持 Azure Active Directory 验证，这样能够利用微软这样的专业服务管理授权认证，尽可能小的避免账户上产生安全问题。如果用户没有 SSO 的环境，才 fallback 到本地账号认证。千万不要认为用三方服务没自己写安全，觉得自己写的逻辑没人知道就不会被黑了，除非你是世界顶级大牛，不然自己写的系统易黑程度远高于三方服务。

另有一些攻击通常由一些敌对阵营的无聊程序员发起，例如使用脚本或工具持续不断的请求博客系统的某个 URL，企图像 DDOS 那样击爆服务器，对于这种无聊刷刷党，博客系统设计者只要加入有关 URL endpoint 的 rate limit 即可。对于真实的 DDOS 攻击，只有云端抗 DDOS 服务或硬件 DDOS 防火墙才能解决。

最后别忘了 OWASP 里没有的东西，博客的协议也会有设计缺陷，例如 pingback 可以用来 DDOS（[https://www.imperva.com/blog/wordpress-security-alert-pingback-ddos/](https://www.imperva.com/blog/wordpress-security-alert-pingback-ddos/)），也能扫描服务器端口（[https://www.avsecurity.in/wordpress-xml-rpc-pingback-vulnerability/](https://www.avsecurity.in/wordpress-xml-rpc-pingback-vulnerability/)）

## 结束语

设计一个优秀的博客系统，每一处细节都值得斟酌。这些设计绝对不可能一开始就能做对，而是得靠长期运营博客的数据去发现并思考。并且，市场会变化，用户行为会变化，标准会被淘汰，也会被发明，因此你的系统需要跟着进化。

任何看似简单的系统，就算普通到烂大街，也有背后看不见的一套完整体系。博客如此，电子商城、外卖、金融清算系统更是复杂，不要光凭自己表面看到的就开始做。就如同造飞机，造个纸飞机和真飞机，绝对不是一回事。

技术人员也不要觉得什么流行就得用什么，优秀的产品并不是堆砌时髦技术做出来的，而先得分析你的用户到底是怎么用你的产品，才能做最合适的选择。要记住，想要一件事情做成功，思路不要只局限于技术本身，学会分析市场，用户行为，才能更准确的选择和应用技术。

![图 | 网络](https://img1.dotnet9.com/2022/03/0607.gif)

感谢读到这里的读者，如果大家有什么疑问和讨论，欢迎留言交流。

**下篇将主要介绍【设计博客系统有哪些知识点】欢迎关注**

![汪宇杰](https://img1.dotnet9.com/2022/03/0307.png)
