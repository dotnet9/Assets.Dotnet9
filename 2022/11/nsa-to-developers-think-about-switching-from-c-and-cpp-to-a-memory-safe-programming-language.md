---
title: 为了避免内存攻击，美国国家安全局提倡Rust、C#、Go、Java、Ruby 和 Swift，但将 C 和 C++ 置于一边
slug: nsa-to-developers-think-about-switching-from-c-and-cpp-to-a-memory-safe-programming-language
description: 对于许多开发人员来说，这可能意味着转向 C#、Go、Java、Ruby、Rust 和 Swift。
date: 2022-11-16 07:31:26
copyright: Original
draft: False
cover: https://img1.dotnet9.com/2022/11/cover_03.png
categories: 分享
tags: 科技生活
---

先丢出美国国家安全局的一篇文章：[NSA Releases Guidance on How to Protect Against Software Memory Safety Issues](https://www.nsa.gov/Press-Room/News-Highlights/Article/Article/3215760/nsa-releases-guidance-on-how-to-protect-against-software-memory-safety-issues/)：

![](https://img1.dotnet9.com/2022/11/0301.png)

本文翻译自两篇文章，第一篇是对美国国家安全局在“软件内存安全”网络安全信息表的解读，第二篇是普及什么是内存安全，为什么它很重要？


## 第一篇 为了避免内存攻击，美国国家安全局提倡Rust、C#、Go、Java、Ruby 和 Swift，但将 C 和 C++ 置于一边

> 本文来自翻译(谷歌翻译加持)。
>
> 原文作者： Liam Tung
>
> 原文标题：NSA to developers: Think about switching from C and C++ to a memory safe programming language
>
> 原文链接：https://www.zdnet.com/article/nsa-to-developers-think-about-switching-from-c-and-cpp-to-a-memory-safe-programming-language/

![Image: Getty Images/iStockphoto](https://img1.dotnet9.com/2022/11/cover_03.png)

>对于许多开发人员来说，这可能意味着转向 C#、Go、Java、Ruby、Rust 和 Swift。

美国国家安全局 (NSA) 敦促开发人员转向内存安全语言——例如 C#、Go、Java、Ruby、Rust 和 Swift——以保护他们的代码免受远程代码执行或其他黑客攻击。

在上面提到的语言中，Java 是企业和 Android 应用程序开发中使用最广泛的语言，而 Swift 是前 10 名语言，部分归功于 iOS 应用程序开发。在系统编程中，人们越来越关注 Rust 作为 C 和 C++ 的替代品。  

“美国国家安全局建议组织考虑在可能的情况下从提供很少或不提供内在内存保护的编程语言（例如 C/C++）到内存安全语言的战略转变。内存安全语言的一些示例包括 C#、Go、Java、Ruby和Swift。”美国国家安全局说。

该间谍机构援引谷歌和微软最近的研究表明，[他们 70% 的安全问题](https://www.zdnet.com/article/microsoft-70-percent-of-all-security-bugs-are-memory-safety-issues/)（分别在 [Chrome](https://www.zdnet.com/article/chrome-70-of-all-security-bugs-are-memory-safety-issues/)和 Windows 中）与内存相关，其中许多是使用 C 和 C++ 的结果，它们更容易出现基于内存的漏洞.

另外： [网络安全、云和编码：为什么这三项技能将在 2023 年引领需求](https://www.zdnet.com/article/cybersecurity-cloud-and-coding-why-these-three-skills-will-lead-demand-in-2023/)

[美国国家安全局在“软件内存安全”网络安全信息表](https://media.defense.gov/2022/Nov/10/2003112742/-1/-1/0/CSI_SOFTWARE_MEMORY_SAFETY.PDF)中指出：“恶意网络行为者可以利用这些漏洞进行远程代码执行或其他不利影响，这通常会危及设备并成为大规模网络入侵的第一步。 ”

“C 和 C++ 等常用语言在内存管理方面提供了很大的自由度和灵活性，同时严重依赖程序员对内存引用执行所需的检查。”

因此，该机构建议尽可能使用内存安全语言，无论是用于应用程序开发还是系统编程。 

“NSA 建议尽可能使用内存安全语言，”它指出。

虽然大多数信息安全专家都熟悉这场关于内存安全语言的争论，但也许并非所有开发人员都熟悉。不过，考虑到这是一个存在了几十年的问题，也许他们应该这样做，正如 Java 的创建者 James Gosling 最近在一次关于[如何以及为什么创建 Java](https://www.zdnet.com/article/programming-languages-java-founder-james-gosling-reveals-more-on-java-and-android/) 的讨论中指出的那样。 

如果有的话，NSA 文件为开发人员提供了一个清晰、通俗易懂的语言解释，说明了转向内存安全语言背后的技术原因。在内存安全方面讨论最多的语言可能是 Rust，它是 C 和 C++ 的“替代品”的主要候选语言。 

继 Android 开源项目之后，Linux 内核 [最近将 Rust 作为 C 的第二语言引入](https://www.zdnet.com/article/linus-torvalds-rust-will-go-into-linux-6-1/)。这些项目不会替换旧的 C/C++ 代码，但会更喜欢 Rust 作为新代码。此外，Microsoft Azure CTO Mark Russinovich 最近呼吁[所有开发人员在所有新项目中使用 Rust 而不是 C 和 C++](https://www.zdnet.com/article/programming-languages-its-time-to-stop-using-c-and-c-for-new-projects-says-microsoft-azure-cto/)。 

“通过利用这些类型的内存问题，不受软件使用正常预期约束的恶意行为者可能会发现他们可以向程序输入不寻常的输入，导致以意想不到的方式访问、写入、分配或释放内存，”美国国家安全局解释道。 

但是——正如专家在[关于 Rust 和 C/C++ 的辩论](https://www.zdnet.com/article/programming-languages-its-time-to-stop-using-c-and-c-for-new-projects-says-microsoft-azure-cto/)中指出的那样 ——美国国家安全局警告说，简单地使用内存安全语言并不能默认排除将内存错误引入软件。此外，语言通常允许不是用内存安全语言编写的库。

“即使使用内存安全语言，内存管理也不完全是内存安全的。大多数内存安全语言都认识到软件有时需要执行不安全的内存管理功能才能完成某些任务。因此，可以使用被认为是非内存安全并允许程序员执行可能不安全的内存管理任务，”美国国家安全局说。 

“一些语言要求任何内存不安全的东西都被显式注释，以使程序员和程序的任何审阅者意识到它是不安全的。内存安全语言也可以使用以非内存安全语言编写的库，因此可以包含不安全的内存功能. 尽管这些包含内存不安全机制的方法颠覆了固有的内存安全性，但它们有助于定位可能存在内存问题的位置，从而允许对这些代码部分进行额外的审查。

**另外： [网络安全：这些是 2023 年需要担心的新事物](https://www.zdnet.com/article/cybersecurity-these-are-the-new-things-to-worry-about-in-2023/)**

NSA 指出，某些内存安全语言可能会以性能成本为代价，这需要开发人员学习一门新语言。它还指出开发人员可以采取一些措施来强化非内存安全语言。例如， Google 的 Chrome 团队正在探索[多种强化 C++ 的](https://www.zdnet.com/article/programming-languages-how-google-is-improving-c-memory-safety/)方法，但这些方法也会带来性能开销。在可预见的未来，C++ 将保留在 Chrome 的代码库中。     

NSA 建议进行静态和动态应用程序安全测试以发现内存问题。它还建议探索内存强化方法，例如 Control Flow Guard (CFG)，这将限制代码的执行位置。同样，建议使用地址空间布局随机化 (ASLR) 和数据执行保护 (DEP)。

## 第二篇 什么是内存安全，为什么它很重要？

> 本文来自翻译(谷歌翻译加持)。
>
> 出处：https://www.memorysafety.org
>
> 原文标题：What is memory safety and why does it matter?
>
> 原文链接：https://www.memorysafety.org/docs/memory-safety/

内存安全是一些编程语言的一个属性，它可以防止程序员引入与内存使用方式相关的某些类型的错误。由于内存安全漏洞通常是安全问题，因此内存安全语言比非内存安全语言更安全。

内存安全语言包括 Rust、Go、C#、Java、Swift、Python 和 JavaScript。内存不安全的语言包括 C、C++ 和汇编语言。

### 内存安全漏洞的类型

为了开始理解内存安全漏洞，我们将考虑一个为许多用户维护待办事项列表的应用程序示例。我们将了解几种最常见的内存安全错误类型，它们可能发生在内存不安全的程序中。

#### 越界读写

如果我们有一个包含十项的待办事项列表，而我们要求第十一项，会发生什么？显然我们应该收到某种错误。如果我们要求否定的第一项，我们也应该得到一个错误。

在这些情况下，内存不安全的语言可能允许程序员读取列表的有效内容之前或之后恰好存在的任何内存内容。这称为越界读取。列表第一项之前的内存可能是其他人列表的最后一项。列表最后一项之后的内存可能是其他人列表的第一项。访问此内存将是一个严重的安全漏洞！程序员可以通过仔细检查他们要求的项目的索引与列表的长度来防止越界读取，但是程序员会犯错误。最好使用一种内存安全语言，默认情况下可以保护您和您的用户免受此类错误的侵害。

在内存安全语言中，我们会在编译时出错或在运行时崩溃。程序崩溃看似严重，但总比让用户窃取彼此的数据要好！

一个密切相关的漏洞是越界写入。在这种情况下，假设我们试图更改待办事项列表中的第十一项或否定的第一项。现在我们正在改变别人的待办事项清单！

#### 释放后使用

想象一下，我们删除了一个待办事项列表，然后请求该列表的第一项。显然我们应该收到一个错误，因为我们不应该能够从已删除的列表中获取项目。内存不安全的语言允许程序获取他们已经完成的内存，现在可以将其用于其他用途。内存中的位置现在可能包含其他人的待办事项列表！这称为释放后使用漏洞。

### 内存安全漏洞有多普遍？

极其。最近的[一项研究](https://langui.sh/2019/07/23/apple-memory-safety/)发现，iOS 和 macOS 中 60-70% 的漏洞是内存安全漏洞。[Microsoft 估计](https://msrc-blog.microsoft.com/2019/07/18/we-need-a-safer-systems-programming-language/)，在过去十年中，其产品中的所有漏洞中有 70% 是内存安全问题。[谷歌估计](https://security.googleblog.com/2019/05/queue-hardening-enhancements.html) 90% 的 Android 漏洞都是内存安全问题。[对被发现广泛被利用的 0-day 的分析](https://twitter.com/LazyFishBarrel/status/1129000965741404160)发现，超过 80% 的被利用漏洞是内存安全问题1。

2003 年的[Slammer 蠕虫](https://en.wikipedia.org/wiki/SQL_Slammer)(这是一种专门衡量软件漏洞的方法，它不包括诸如凭据网络钓鱼之类的非常普遍的事情。)是缓冲区溢出（越界写入）。[WannaCry](https://www.fireeye.com/blog/threat-research/2017/05/smb-exploited-wannacry-use-of-eternalblue.html)（越界写入）也是如此。针对 iPhone的[Trident 漏洞](https://blog.lookout.com/trident-pegasus-technical-details)利用了三个不同的内存安全漏洞（两个释放后使用和一个越界读取）。[HeartBleed](https://tonyarcieri.com/would-rust-have-prevented-heartbleed-another-look)是内存安全问题（越界读取）。[Android 上的Stagefright](https://googleprojectzero.blogspot.com/2015/09/stagefrightened.html)也是如此（越界写入）。glibc 中的[Ghost漏洞](https://blog.qualys.com/laws-of-vulnerabilities/2015/01/27/the-ghost-vulnerability)？你打赌（越界写）。

由于 C 和 C++ 不是内存安全的，因此这些漏洞以及利用其他漏洞成为可能。编写大量 C 和 C++ 的组织不可避免地会产生大量漏洞，这些漏洞可直接归因于缺乏内存安全性。这些弱点被利用，给[医院](https://www.bbc.com/news/technology-41753022)、[持不同政见者](https://citizenlab.ca/2016/08/million-dollar-dissident-iphone-zero-day-nso-group-uae/)和[卫生政策专家](https://citizenlab.ca/2017/02/bittersweet-nso-mexico-spyware/)带来危险。使用 C 和 C++[对社会](https://www.telegraph.co.uk/technology/2018/10/11/wannacry-cyber-attack-cost-nhs-92m-19000-appointments-cancelled/)不利，[对您的声誉](https://www.zdnet.com/article/qualpwn-vulnerabilities-in-qualcomm-chips-let-hackers-compromise-android-devices/)不利，[对您的客户](https://www.nytimes.com/2018/12/02/world/middleeast/saudi-khashoggi-spyware-israel.html)不利。

### 与内存不安全的语言相关的还有哪些其他问题？

内存不安全的语言也会对稳定性、开发人员生产力和应用程序性能产生负面影响。

由于内存不安全的语言往往会出现更多错误和崩溃，因此会极大地影响应用程序的稳定性。即使崩溃不是安全敏感的，它们对用户来说仍然是非常糟糕的体验。

更糟糕的是，开发人员很难追踪到这些错误。内存损坏通常会导致崩溃发生在距离错误实际位置很远的地方。当涉及多线程时，线程运行时间的微小差异可能会触发其他错误，从而导致更难重现错误。结果是开发人员通常需要盯着崩溃报告看几个小时才能确定内存损坏错误的原因。这些错误可能几个月都没有修复，开发人员完全相信存在错误，但不知道如何在发现其原因和修复方面取得进展。

最后，还有性能。在过去的几十年里，人们可以指望 CPU 每一两年都变得更快。这已不再是这种情况。相反，CPU 现在带有更多内核。为了利用额外的内核，开发人员需要编写多线程代码。

不幸的是，多线程加剧了与缺乏内存安全相关的问题，因此，在 C 和 C++ 中利用多核 CPU 的努力通常是棘手的。例如——在最终（成功）用多线程 Rust 重写系统之前，Mozilla 多次尝试将多线程引入 Firefox 的 C++ CSS 子系统，但均以失败告终。

### 正确的前进道路是什么？

使用内存安全语言！有很多很棒的可供选择。编写操作系统内核或 Web 浏览器？考虑Rust！为 iOS 和 macOS 构建？Swift可以胜任。网络服务器？Go 是个不错的选择。这些只是几个例子，还有许多其他优秀的内存安全语言可供选择（以及许多其他精彩的用例配对！）。

更改您的组织使用的编程语言并非轻而易举。这意味着改变你在招聘时寻找的技能，这意味着对你的员工进行再培训，这意味着重写大量代码。尽管如此，我们相信从长远来看这是必需的，因此我们想说明为什么采用新编程语言的替代方案没有成功。

如果我们理所当然地认为使用不安全的语言会产生一些漏洞，那么我们想问的问题是：我们是否可以采取一些技术来降低这种风险，而不用强迫自己完全改变编程语言？答案是肯定的。并非所有用不安全语言编写的项目都同样不安全和不可靠。

一些可以降低使用不安全语言风险的做法是：

- 使用一些[现代 C++ 惯用语](https://alexgaynor.net/2019/apr/21/modern-c++-wont-save-us/)可以帮助生成更安全可靠的代码
- 使用[fuzzers](https://llvm.org/docs/LibFuzzer.html)和[sanitizers](https://clang.llvm.org/docs/AddressSanitizer.html)帮助在将错误投入生产之前找到它们
- 使用漏洞利用缓解措施来帮助增加利用漏洞的难度
- 权限分离，即使漏洞被利用，爆炸半径也更小

这些做法有意义地降低了使用不安全语言的风险，如果我们未能说服您更换语言，而您打算继续编写 C 和 C++，那么采用这些做法势在必行。不幸的是，它们也严重不足。

浏览器和操作系统开发人员是开发现代 C++ 惯用语、[fuzzers](https://llvm.org/docs/LibFuzzer.html)、[sanitizers](https://clang.llvm.org/docs/AddressSanitizer.html)、漏洞利用缓解和权限分离技术的最前沿人员——正是我们在开始时通过有关内存安全问题普遍性的统计数据强调的群体。尽管这些团队在这些技术上进行了投资，但他们使用不安全的语言却拖累了他们。在大型黑客大赛pwn2own上，2019年这些产品被[利用的漏洞](https://twitter.com/LazyFishBarrel/status/1110021027851964417)中有一半以上是由于缺乏内存安全，除了一个例外，[每一次成功的攻击](https://twitter.com/LazyFishBarrel/status/1110023874396078081)都至少利用了一个内存安全漏洞。

### 放弃 C 和 C++ 真的可行吗？

希望到现在为止，我们已经让您相信，像 C 和 C++ 这样的不安全语言是我们产品中大量不安全的根本原因，并且尽管您可以采取一些措施来降低风险，但您无法接近消除它。所有这些可能仍然让你觉得改变你使用的编程语言，产生数百万行代码，是一个压倒性的巨大任务。通过将其分解为可管理的部分，我们可以开始取得进展——我们的目标不是大爆炸式地重写世界，而是在降低风险方面取得进展。

首先是全新的项目。对于这些，您可以选择简单地使用内存安全语言。这些项目的风险最低，因为您不需要从重写任何代码开始，尽管像这样的项目通常确实需要改进测试或部署基础设施以支持新的编程语言。这是 ChromeOS 的 CrosVM（操作系统的全新组件）所采用的方法。

如果您没有新项目，下一个寻找机会使用内存安全语言的地方是现有项目的新组件。一些内存安全语言对与 C 和 C++ 代码库（例如 Rust 和 Swift）的互操作提供了一流的支持。这需要稍高的初始投资，因为它需要集成到构建系统中，以及使用新语言为需要跨越两种语言之间的边界传递的对象和数据构建抽象。当[WebAuthn](https://blog.mozilla.org/security/2019/03/19/passwordless-web-authentication-support-via-windows-hello/)作为 Firefox 的一个新组件实现时，以及一个支持[在 Rust 中编写 Linux 内核模块](https://github.com/fishinabarrel/linux-kernel-module-rust)的项目，就成功地使用了这种策略。

前两种方法的共同点是它们都处理新代码。这具有与现有代码定义明确的交互点的优势，并且无需重写任何内容即可开始工作。它还为您提供了止血的机会：没有使用不安全语言的新组件，我们将逐步处理现有代码。对于没有任何自然的新组件来开始使用内存安全语言的项目，采用更具挑战性。

在这种情况下，您需要寻找一些现有组件以将不安全语言重写为安全语言。最好是您选择的组件是您已经考虑重写的组件：可能是为了性能、安全性，或者是因为代码太难维护。你应该尝试为你的第一次内存安全重写选择范围尽可能小的东西，以帮助项目成功并尽快发布；这有助于将重写中固有的风险降至最低。Stylo，用 Rust 重写了 Firefox 的 CSS 引擎，是这种方法的一个成功例子。

无论哪种方法最适合您的组织，都需要牢记一些事项以最大程度地提高成功的机会。首先是确保你有内部支持者和高级工程师，他们可以使用对许多团队成员来说都是新的语言提供代码审查和指导。这样做的自然延伸是确保将使用新语言工作的工程师有可用的资源，如书籍、培训或内部指南。最后，您需要确保新语言拥有与旧语言相同的共享基础设施，例如构建系统、测试、部署、崩溃报告和其他集成。

### 结论

采用一种新的编程语言并开始迁移到它的过程并不是一件容易的事。它需要整个组织的规划、资源配置和最终投资。如果我们不必考虑这些事情，生活会容易得多。不幸的是，对数据的审查清楚地表明，我们根本不能考虑继续为安全敏感项目使用不安全的语言。

数据一次又一次地证明，当项目使用 C 和 C++ 等不安全语言时，它们就会受到大量安全漏洞的困扰。无论工程师多么有才华，在权限减少和利用缓解方面的投资有多大，使用内存不安全的语言只会导致太多错误。这些错误大大降低了安全性、稳定性和生产力。

幸运的是，我们不需要满足于现状。在过去的几年中，出现了 C 和 C++ 的绝佳替代品，例如 Rust、Swift 和 Go 等。这意味着我们不必在未来的许多年里将内存损坏漏洞作为一个信天翁挂在脖子上，只要我们选择不这样做。我们期待有一天，选择使用不安全的语言被认为是疏忽大意，因为没有多因素身份验证或没有加密传输中的数据。

### 感谢亚历克斯盖纳

经许可，此解释基于 Alex Gaynor 的博客文章[工程副总裁的内存不安全简介](https://alexgaynor.net/2019/aug/12/introduction-to-memory-unsafety-for-vps-of-engineering/)。

## 本文总结

希望大家能从这两篇译文中得到些什么。