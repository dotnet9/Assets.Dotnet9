---
title: Flutter稳定支持Windows，开发者做好准备了吗？
slug: Flutter-supports-windows-stably-Are-developers-ready
description: Flutter是由谷歌开发的开源移动UI框架，可快速在不同平台上构建高质量原生用户界面。
date: 2022-02-07 22:24:11
copyright: Reprinted
author: 技术视野
originalTitle: Flutter稳定支持Windows，开发者做好准备了吗？
originalLink: https://blog.csdn.net/csdndevelopers/article/details/122808516
draft: False
cover: https://img1.dotnet9.com/2022/02/0707.png
categories: 前端
tags: Flutter
---

Flutter 是由谷歌开发的开源移动 UI 框架，可快速在不同平台上构建高质量原生用户界面。Flutter 支持现有的所有代码，在世界各地受到越来越多开发者的追捧。到目前为止，全球已发布了近 50 万个使用 Flutter 的应用程序，其中包括来自字节跳动等大型公司的应用程序，以及谷歌三十个团队的应用程序。据 Statista 和 SlashData 的分析师表示，Flutter 是 2021 年最受欢迎的跨平台 UI 工具。

> 整理 | 郭露
>
> 出品 | CSDN（ID：CSDNnews）

2 月 4 日，Flutter 稳定版 2.10 重磅推出，该版本在春节期间发布，距离上次的发布还不到两个月。但即使在这么短的时间内，我们已经关闭了 1843 个 issues 以及来自全球 115 名贡献者的 1525 个 PR。此次版本更新包括 Flutter 对 Windows 的重大支持更新、部分性能改进、不同平台的新增功能以及一些工具的改进。

![图源自Flutter](https://img1.dotnet9.com/2022/02/0701.png)

## Flutter 2.10 新特性一览

### 1. 性能改进

Flutter 2.10 版本初步支持由 Flutter 社区成员 knopp 提供的绘制脏区管理功能。他为 iOS/Metal 上的单个脏区域启用了选择性重绘。这将大大缩短部分基准测试中的九十分位和九十九分位的光栅化时间，并将这些基准测试中的 GPU 占用率从 90%以上降低到 10%以下。

除此之外，该版本还对图片格式进行了优化。开发者可以更有效地调节图层透明度。基准测试中每帧光栅化时间至少缩短了三分之一。

在 profile 和 release 模式下，Dart 代码以 AOT 方式进行编译。这段代码解锁了许多编译器优化和激进的 tree-shaking。但是由于类型流分析必须涵盖整个程序，因此对性能有些影响。2.10 版本带来了更快的类型流分析实现。在基准测试中，Flutter 应用程序的总体构建时间缩短了约 10%。

### 2. iOS 更新

除了性能改进之外，Flutter 2.10 版本还在各平台增加了不同的增强功能。例如开发者 luckysmg 带来了流畅的 iOS 键盘动画、iOS 相机插件的稳定性提高，以及为 64 位架构的 iOS 系统加入了减少内存使用的新功能——压缩指针。

### 3. Android 更新

同时 Flutter 2.10 版本针对 Android 进行了改进。当开发者创建新应用时，Flutter 可支持最新版本的 Android，即 Android 12。此外，在此版本中，我们启用了 MultiDex。如果开发者使用的是低于 21 的 Android SDK 版本，并超过了 64K 方法数限制，只需将 --multidex 传递给 flutter build appbundle 或是 flutter build apk ，就能够支持 MultiDex。

### 4. Web 更新

Flutter 版本中同样包括对于 Web 的改进。在之前的版本中，当鼠标拖动到多行文本框的边缘时，它不会同步滚动。而在 2.10 版本中，选择光标拖出了文本框后，文本框会进行滚动，浏览并选中对应的内容。该功能适用于 Wed 和桌面应用程序。

### 5. Material 3

Flutter 2.10 是向 Material 3 过渡的开始，其中包括从单一颜色生成成整个配色方案的功能。

开发者可以使用任意颜色创建新的 ColorScheme 类型。ThemeData 的构造函数还包含一个新的 colorSchemeSeed 参数，可以直接从颜色生成主题。此外，该版本包括了 ThemeData.useMaterial3 的参数，可将 Widget 切换到新的 Material 3 外观。
Flutter 还增加了 1028 个新 Material 图标。

![图源自Flutter](https://img1.dotnet9.com/2022/02/0702.png)

除上述功能以外，Flutter 2.10 还在集成测试、DevTools 以及 VSCode 等进行了改进，并彻底移除了 Dev 渠道以及对 iOS 9.3.6 支持。

对于此次版本更新，最引人关注的莫过于稳定支持 Windows 应用开发，对此，Flutter 产品经理蒂姆·斯内斯（Tim Sneath）发文进行了详细解读，让我们一起来看一下。

## 已为 Windows 做好准备的 Flutter

![图源自Flutter官网](https://img1.dotnet9.com/2022/02/0703.png)

Flutter 旨在创建高效跨平台软件框架，在过去几年取得了长足发展。Flutter 可为 Android、iOS、Linux、Windows、macOS 以及网页开发应用，并兼容现有的所有代码。受到了全球各地区开发者的支持信赖。

谷歌表示：“今天标志着这一愿景的重大扩展，首次发布了对 Windows 作为应用目标的支持，使 Windows 开发者能够受益于移动开发者一直享有的同样的生产力和力量”。

为实现这一目标，谷歌一直致力于 Flutter 开发。五年前，谷歌曾推出 Flutter Alpha，该版本大大提高了移动操作系统的开发速度。Flutter 应用程序可使用 Dart 编写，实现了在 Android、iOS、Windows、macOS 和 Linux、Web 以及嵌入式设备上运行。

然而， 要想实现 Flutter 桌面支持并非易事，必须重新针对 Windows 进行设计，桌面应用需要兼容键盘和鼠标等不同硬件以及不同大小的屏幕，同时对于输入法、视觉样式等也有不同的需求，还要支持文件系统选择器以及 Windows 注册表等数据存储的各种内容。

正如 Flutter 对 Android 和 iOS 的支持一样，Flutter 的 Windows 结合了 Dart 框架以及 C++ 引擎。Windows 和 Flutter 通过嵌入层进行通信，该嵌入层承载 Flutter 引擎并负责翻译和分发 Windows 信息。Flutter 与 Windows 可将 UI 绘制到屏幕上，并与现有的 Windows 模式相配合。

![图源来自Flutter和Dart产品经理博客](https://img1.dotnet9.com/2022/02/0704.png)

开发者可在 Windows 上使用 Flutter 框架的所有功能，并通过 Dart 或 C++ 编写的平台插件与 Win32、COM 和 Windows Runtime API 进行通信，同时 Flutter 团队还对许多常用插件进行调整以支持 Windows，其中包括 camera、file_picker 以及 shared_preferences 。更重要的是，Flutter 社区中还添加了大量对其他包的 Windows 支持，其中涵盖了从 Windows 任务栏集成到串行端口访问的所有内容 。

已有数百个包支持为 Windows 构建的 Flutter 应用程序

![图源来自Flutter](https://img1.dotnet9.com/2022/02/0704.png)

对于完全定制的 Windows UI，您还可以使用包 fluent_ui 来 flutter_acrylic 创建一个可以精美地表达 Microsoft Fluent 设计系统的应用程序。使用该 msix 工具，您可以将您的应用程序包装在一个安装程序中，该安装程序可以上传到 Windows 上的 Microsoft Store。

总的来说，Flutter 2.10 的推出实现了在 Windows 上的快速运行，并且可以传输到其他桌面或移动设备以及 Web。以下是早期示例：

![图源来自Flutter](https://img1.dotnet9.com/2022/02/0706.png)

![图源来自Flutter](https://img1.dotnet9.com/2022/02/0707.png)

该版本推出后，微软 Windows 开发者平台公司副总裁 Kevin Gallo 表示：”我们很高兴看到 Flutter 实现对创建 Windows 应用程序的支持。作为一个开放平台，Windows 欢迎所有开发人员的加入。Flutter 能实现 Windows 应用支持并在 Microsoft Store 上架，表明其对我们的信任，期待 Flutter 在 Windows 上的发展！”

除此之外，许多 Flutter 合作伙伴也在增加对 Windows 的支持，其中包括：

作为低代码 Flutter 应用程序设计工具，FlutterFlow 宣布支持 Windows 并将帮助 Flutter 开发人员构建专为桌面使用的功能。

本地数据存储工具 Realm 的最新版本支持使用 Flutter 构建 Windows 应用程序，使用 Dart FFI 快速访问底层数据库，并增加了对 iOS 和 Android 等移动平台的现有支持。

Nevercode 已更新 Codemagic CI/CD 工具以支持 Windows，使用户能够在云中测试并构建 Windows 应用程序，并自动部署到 Microsoft Store。

Syncfusion 已更新小部件支持 Windows。数据可视化组件等支持创建 PDF 文件和 Excel 表格。

Rive 宣布了其图形工具套件即将推出 Windows 版本，开发人员可创建可以使用状态机实时响应代码的交互式矢量动画。他们还将推出 Windows 应用程序，在性能和内存上有着不俗的表现，很快将在 Microsoft Store 中提供下载。

目前看来，大家对于 Flutter 2.10 的评价依旧非常好。你对于 Flutter 2.10 的发布有什么期待呢？欢迎在下方留言。

**【参考资料】**

- [https://www.theregister.com/2022/02/04/flutter_windows_production_release/](https://www.theregister.com/2022/02/04/flutter_windows_production_release/)
- [https://medium.com/flutter/announcing-flutter-for-windows-6979d0d01fed](https://medium.com/flutter/announcing-flutter-for-windows-6979d0d01fed)
- [https://www.mongodb.com/developer/article/introducing-realm-flutter-sdk/](https://www.mongodb.com/developer/article/introducing-realm-flutter-sdk/)
- [https://medium.com/flutter/whats-new-in-flutter-2-10-5aafb0314b12](https://medium.com/flutter/whats-new-in-flutter-2-10-5aafb0314b12)

> 版权声明：本文为 CSDN 博主「技术视野」的原创文章，遵循 CC 4.0 BY-SA 版权协议，转载请附上原文出处链接及本声明。
>
> 原文链接：https://blog.csdn.net/csdndevelopers/article/details/122808516
