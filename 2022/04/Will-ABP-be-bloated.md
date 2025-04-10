---
title: ABP会臃肿吗
slug: Will-ABP-be-bloated
description: 我有时候在想在JAVA领域，Spring基本一统天下，新手也好，高手也罢都在学习、研究和项目实战。
date: 2022-04-24 20:44:57
copyright: Reprinted
author: 张飞洪[厦门]
originalTitle: ABP会臃肿吗
originalLink: https://www.cnblogs.com/jackyfei/p/16063572.html
draft: False
cover: https://img1.dotnet9.com/2022/04/cover_34.jpg
categories: 
    - ASP.NET Core
tags: 
    - ABP
---

## 1. 有了 ABP，还要学其他的框架？

我有时候在想在 JAVA 领域，Spring 基本一统天下，新手也好，高手也罢都在学习、研究和项目实战。也就是说其实对与应用开发，Spring 已经是绕不开的框架，不管是单体还是微服务都可以轻松胜任，学了 Spring 在 JAVA 开发层面，其实你可以完全不需要去学习其他的任何知识点，也能参加工作（当然不包括数据库和前端，我这里指的是 JAVA 语言本身）。

那么在.NET 有没有类似 Spring 的框架，让.NET 学员学完之后也可以胜任任何.NET 工作，甚至能胜任架构师的角色，我觉得对于开源的 ABP vNext 来说，是完全可以做到的。因为它足够简单，上手非常容易，只要入门之后持续一段时间，我们就可以不断地在实战中逐步深入，重要的是开源的信息量足够一个架构师去模仿和学习，不管内部的模块化设计理念，还是模块化安装和删除的方式，都是一个理念活生生的落地代码，看得见摸得着，甚至我有时候觉得，只要学好 ABP 其他的框架可学可不学，不学是因为其他的框架从综合素质来说，目前还没有看到一个框架在应用层面可以和 ABP 媲美（.NET 领域），没有夸张地说没有之一。学是因为不管是任何开源的框架都可以被 ABP 吸收并蓄，只要我们自己懂得 ABP 的框架规则，我们的集成是分分钟的事情。

所以，我的结论是，如果你们公司的业务是垂直产品的，可以用 ABP 没问题，因为它可以从模块到单体到微服务兼容，到微服务做到快速而高效的演化；如果你们公司的业务是项目型的交付，也可以重复利用 ABP 的模块化来降低开发的成本；如果你们公司是外包型的项目，面对交付各种各样，ABP 一样可以通过排列组合来快速交付成果。我这里不是故意夸大，也不是为了宣传而宣传，而是真的觉得这玩意儿真的是很方便，谁用谁知道。

## 2. ABP 难吗？

当然 ABP 入门容易，进阶困难，毕竟他是基础设施，里面有很深厚的技术深度，如果你只是停留在使用的角度，那么问题不大，可以很快入门，请相信我，对于有点.NET 基础的人，可能一天就上手了，但是一旦遇到问题或者需要定制，那么你一定会遇到困难，这个时候你会迷失在碎片化的模块海洋，而且每个模块之间又有网状般的依赖关系，模块化是非常灵活的，一个解决方案你要分 4 层还是 7 层，甚至更多都是很有讲究的，因为你要深入理解 DDD 分层模型和业务需求。所以，当你需要深入学习的时候，你会觉得 ABP 不容易。

## 3. ABP 存在的意义？

问这个问题就好比问我们有了 Java，为什么还要有 Spring。ABP 存在真正的意义是让开发人员早点下班，做一个幸福的程序员。不要觉得这个要求很低，这非常困难。因为项目从开发到运维没有一个环节是容易的。项目初期，我们会为了进度加班加点，虽然累但不感觉疲惫，因为项目一直在推进。项目后期，你不断修改代码也可以接受，因为都是你自己写的代码，你知道怎么修改。项目运维阶段，你离职了，新人一边修改你的代码一边摇头骂娘，项目开始慢慢变质。随着一波又一波的新人不断折腾和累积屎山，你的项目遍体鳞伤、伤痕累累确又无法推倒重来，代码开始慢慢腐朽。

所有的这些都是因为团队缺乏规范，人员层次不齐，因为你没有一个行业最佳实践的地图。如果说 ABP 能帮助我们解决以上这些问题，那么它的价值自然不低。从事.NET 开发人员如果您跳槽频繁的话，我相信你一定接手过很多遗留系统，很多系统从构建的第一天可能有了遗留系统的痕迹，所以如果我们要进行整体迁移，那就是一场噩梦，我干了 10 来年的.NET，真的还没见过成功的案例，要么团队能力不够，要么成本太高，要么风险太大……总之，一切问题都在第一天就定了基调。

除了项目管理和软件工程方面，在技术方面我们一样可以从开源的 ABP 身上学到不少经典代码、架构设计、测试规范、最佳实践。我举一个简单的多租户的例子，如果你有在用多租户，你一定能对它的多租户印象深刻，因为你好像感觉不到多租户的存在，也许这就是多租户的优雅之处。至于 DDD 就更不用说了，你跟着它的规范走一遍，你对 DDD 会有更加深入的了解，原来我们以前一直在用的贫血模式开发就觉得自己懂得了 DDD。

ABP 的意义真的不是三言两语能说的完的，毕竟它自己也不断与时俱进中，它本身从开源身上吸收的能量也在间接展示给我们看。我到现在都觉得自己不能精通 ABP，因为它上手容易，精通真的不是那么容易，因为这意味着你曾经花费很大的时间从 0 到 1 重构了一次 ABP，否则你怎么敢说自己精通 ABP 呢？

## 4. ABP 会臃肿吗？

我经常听到有些人会说 ABP 很臃肿，我不知道他是指的哪一方面，如果只是泛泛而谈，我感觉是很不专业的，甚至我可以说对方根本没有深入研究过 ABP。是 Dll 文件过多，还是启动很慢，还是吃内存，除非你能拿出证据来。

`真正的臃肿是什么？`

模块化是根据业务或者功能的一种拆分，目的是为了重用。拆分多了，你可能学习了会有点困难，因为那么多块块，如果你不够熟悉，身边没有一份说明书，你根本就无从下手。所以，那不是叫臃肿，我们有人会评价乐高很臃肿吗？相反，我们会觉得乐高很强大，很灵活，可以实现各种可能，除非你缺乏想象力，不要用自己想象力的缺乏来说乐高是臃肿的。

管理复杂系统必然要求我们对系统进行分类拆分，否则一块大单体岂不是更加臃肿？我理解的臃肿是，代码耦合度非常高，进行二次开发很困难，剪不断理还乱。另外开发完了，要进行运维修改很困难，甚至出现改到后面，改不动只能重新推倒重来，在旧业务 online 情况下，新框架要重新开发，边开飞机边换引擎，中间还要做数据库迁移，对于一般的小公司真的非常危险，严重的会直接宣告项目死刑，因为改不动了，然后新进的人员也只想贴膏药，公司必然存在离职率高的问题。所以，我的结论是代码耦合高是臃肿，二开困难是臃肿，运维低效是臃肿。当然臃肿还有很多内涵，比如编译缓慢、部署麻烦、性能低下等等。

我想请教那些说 ABP 臃肿的人，你到底在说什么？模块化后，团队各自编译会缓慢吗？模块化后，模块可以单独部署也可以集成部署是臃肿吗？你使用 ABP 跑功能会很慢吗，所有的模块都在启动时候就预先加载到我们的内存里了，哪儿慢了？无非多耗费一点内存，多用一点空间换取的不是更快的性能是什么？

所以，请那些说 ABP 臃肿的人，不要泛泛而谈，请你在臃肿前面加个定语，然后用真实的案例来和大家交流。

## 5. 结尾

因为 ABP 很优秀，所以我花了很大的精力对 ABP 进行了深耕，不敢说精通，但是不止一次在深夜探索 ABP 源码，也阅读了许许多多的文档资料，ABP 入门真的很容易，但是如果你想进一步窥探它的神秘面纱，也许我的这个系列翻译应该可以帮你更快、更深入地学习它，祝你学有所得，学得快乐。

如果你也在学习 ABP，也有遇到问题需要咨询，欢迎你加入 ABP 的 QQ 群（免费）

![](https://img1.dotnet9.com/2022/04/3302.png)

或者加入我的[知识星球](https://t.zsxq.com/I2vNFub)（收费），体验更加及时和全面的服务。

> 希望以上分享对你有所帮助，感谢您的捧场。
>
> 作者： [张飞洪[厦门](http://www.cnblogs.com/jackyfei/)
>
> QQ 群： [共享交流群](http://wpa.qq.com/msgrd?v=3&uin=996767213&site=qq&menu=yes)
>
> 我的： [知识星球](https://t.zsxq.com/I2vNFub)
