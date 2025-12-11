---
title: Avalonia UI的演进逻辑与Qt生态深度对比
slug: avalonia-ui-qt-ecosystem-comparison
description: 在软件工程的演进史中，跨平台图形用户界面（GUI）的开发始终是一个充满了妥协、权衡与技术博弈的领域。
date: 2025-12-11 07:26:17
lastmod: 2025-12-11 08:14:35
cover: https://img1.dotnet9.com/2025/12/cover_01.png
copyright: Reprinted
banner: false
author: 张善友MVP dotNET跨平台
originalTitle: Avalonia UI的演进逻辑与Qt生态深度对比
originalLink: https://mp.weixin.qq.com/s/SyhLR8ic-_g-uDEakqCmKw
categories:
  - WPF
  - Avalonia UI
---

![](https://img1.dotnet9.com/2025/12/cover_01.png)

## **一 引言：跨平台图形界面的历史张力与技术真空**

在软件工程的演进史中，跨平台图形用户界面（GUI）的开发始终是一个充满了妥协、权衡与技术博弈的领域。长久以来，开发者被迫在“一次编写，到处运行”的效率愿景与“原生级性能与体验”的质量要求之间做出艰难抉择。在这一漫长的探索周期中，C++与其王牌框架Qt长期占据了工业级、嵌入式及高性能桌面应用开发的统治地位。Qt以其底层的控制力、强大的元对象编译器（MOC）以及对操作系统API的深度抽象，成为了衡量跨平台框架的黄金标准。

然而，随着.NET生态系统的崛起，特别是C[#语言在生产力](javascript:;)、内存安全性以及运行时优化方面的长足进步，一个巨大的市场真空逐渐显现：.NET开发者渴望一个能够媲美Qt的跨平台能力，同时又能保留C[#高效开发体验的UI框架](javascript:;)。微软虽然先后推出了Windows Forms、WPF（Windows Presentation Foundation）、UWP以及最近的MAUI，但这些技术主要聚焦于Windows生态，或在跨平台策略上采取了“包装原生控件”（Wrapper）的路径，导致了平台间表现不一致的顽疾。

正是在这种技术与市场的双重张力下，Avalonia UI应运而生。本文将以Avalonia UI现任首席执行官Mike James的职业历程为叙事线索，深度剖析从Qt时代的工业积淀到Avalonia时代的架构创新。我们将探讨这位曾经的Qt开发者如何将对C++框架的深刻理解转化为.NET生态中的颠覆性力量，并详细对比Avalonia与Qt在渲染引擎、状态管理、绑定机制等核心维度的技术差异。同时，本文将深入研究AvaloniaUI OÜ独特的商业化路径——如何在拒绝外部风险投资的前提下，通过“核心开源+商业闭源（XPF）”的混合模式，成功挑战Qt在嵌入式与企业级市场的霸主地位。

## **二 轨迹的交汇：Mike James与跨平台哲学的演变**

### **2.1 Qt时期的技术洗礼：工业级严谨性的初识**

Mike James的技术生涯始于大学毕业后的第一份工作，彼时他置身于C++与Qt（他习惯称之为“Cute”）构建的跨平台开发环境中 1。在那个时期，Qt已经是跨平台开发的代名词，特别是在对性能要求极高的桌面与嵌入式领域。

这段经历对Mike James产生了深远的影响，主要体现在对“自绘UI”（Drawn UI）架构优势的早期认知。Qt Widgets（当时的主流技术，即“Cute Four”时代）并不依赖操作系统的原生控件来渲染按钮或文本框，而是通过自身的绘图引擎在画布上绘制像素 1。这种设计哲学保证了应用程序在Windows、Linux和macOS上拥有绝对一致的视觉表现和行为逻辑，消除了“原生包装器”常见的布局错位和API差异问题。

然而，C++作为一种系统级语言，其内存管理的复杂性（尽管Qt引入了对象树机制来辅助管理）和编译速度的瓶颈，让Mike James开始思考开发效率的重要性。他提到了自己“错过了Qt转向QML的阶段” 1，这意味着他在Qt生态中的经验主要集中在命令式的C++逻辑上，而非后来引入的声明式UI（QML）。这种“错过”在某种程度上成为了他后来拥抱XAML（.NET生态的声明式语言）的契机，他意识到声明式UI在构建复杂界面时的优越性，但同时也看到了强类型语言在大型工程维护中的必要性。

### **2.2 转向.NET与Xamarin：移动端跨平台的探索与反思**

离开Qt开发环境后，Mike James加入了Xamarin（后被微软收购），这标志着他从C++向C#/.NET生态的彻底转型 1。Xamarin的使命是将.NET带入iOS和Android开发领域，其核心技术是Mono运行时。

在Xamarin和随后的微软任职期间，Mike James深入接触了移动端跨平台开发的另一条技术路线——原生控件包装（Native Wrapping）。Xamarin.Forms（以及后来的.NET MAUI）通过抽象层调用iOS的UIKit和Android的Views。这种方式虽然能提供“原生外观”，但却带来了巨大的维护成本：

- **API“最大公约数”问题**

  ：框架只能暴露所有平台共有的功能，导致特定平台的特性难以利用。

- **渲染不一致**

  ：同一个XAML布局在不同OS上可能有完全不同的渲染结果，导致开发者需要编写大量的平台特定代码（Custom Renderers）来修复UI bug。

这段经历让Mike James更加怀念Qt式的“自绘”模式。他意识到，.NET生态需要一个像Qt一样完全控制渲染管线，但又能利用C[#语言优势的框架](javascript:;)。这为他后来全身心投入Avalonia项目埋下了伏笔。

### **2.3 从贡献者到掌舵人：Avalonia的商业化转型**

Avalonia（原名Perspex）最初由Steven Kirk于2013年创建，旨在创建一个跨平台的WPF 2。Mike James并非创始人，但他作为核心贡献者深度参与了项目的早期发展，并在2019年创立了AvaloniaUI OÜ，出任CEO 3。

Mike James的加入不仅带来了技术上的贡献，更重要的是带来了商业上的战略视野。他面临的核心挑战是：如何在一个由巨头（微软、Google、Qt Company）把持的红海市场中，让一个开源项目生存并盈利？他拒绝了外部风险投资（VC）的诱惑 5，坚持认为引入VC会迫使公司追求短期增长而牺牲开源社区的利益。相反，他制定了一条“服务驱动产品，产品反哺开源”的独特路径，通过为企业解决WPF迁移的痛点（Avalonia XPF）来获取资金，从而维持核心框架的独立性和开源纯度。

## **三 深度技术对决：Avalonia UI vs. Qt 架构解析**

Avalonia常被视为“.NET版的Qt”，二者在设计哲学上有诸多相似之处（如自绘渲染），但在底层架构、语言特性及通信机制上存在本质差异。本章将从微观层面剖析这两大框架的技术基因。

### **3.1 渲染引擎架构：Skia与QPainter的较量**

#### **3.1.1 Qt的渲染演进：从光栅化到场景图**

Qt的渲染历史悠久且复杂，主要分为两个阶段：

- **QPainter (Widgets)**

  ：这是Qt传统的命令式2D绘图引擎 6。它通过统一的API抽象了底层的绘图上下文（Windows GDI, X11, macOS Quartz）。虽然稳定，但在现代高分辨率屏幕和复杂动画场景下，QPainter基于CPU的光栅化能力逐渐显露出瓶颈。

- **Qt Quick / Scene Graph**

  ：随着QML的引入，Qt构建了基于场景图（Scene Graph）的渲染管线。它能够将UI元素构建为节点树，并直接利用底层图形API（OpenGL, Vulkan, Metal）进行GPU加速渲染。

#### **3.1.2 Avalonia的渲染策略：SkiaSharp与平台抽象**

Avalonia采取了更为现代和统一的策略，其渲染架构更接近于Google的Flutter：

- **Skia为核心**

  ：Avalonia默认使用Skia图形库（通过SkiaSharp绑定）作为跨平台渲染后端 7。Skia也是Chrome浏览器和Android界面的渲染引擎，这意味着Avalonia天生具备了高性能的2D矢量绘图能力。

- **像素级控制**

  ：与MAUI依赖原生控件不同，Avalonia通过Skia在操作系统的窗口（Window）上直接绘制所有UI元素。这种“自绘”机制保证了在Linux、Windows、macOS乃至WebAssembly上，像素的渲染结果是完全一致的 8。

- **风险管理与“保险策略”**

  ：Mike James透露，Avalonia团队为了规避SkiaSharp维护者单一的风险，曾在内部开发了自己的Skia绑定 7。这一细节展示了商业公司在技术选型上的成熟度——不仅追求性能，更关注供应链安全。

- **未来架构（Impeller与GPU优先）**

  ：受到Flutter转向Impeller渲染器的启发，Avalonia v12计划引入实验性的GPU优先渲染管线 7。目前的Skia主要依赖CPU进行路径光栅化（尽管可以上传纹理到GPU），在高负载下可能导致掉帧。新的渲染架构旨在消除着色器编译引起的卡顿（Jank），利用GPU的计算能力直接处理几何图形，这将使Avalonia在动画流畅度上进一步逼近甚至超越原生Qt Quick的表现。

### **3.2 通信与状态管理：编译绑定 vs. 信号与槽**

#### **3.2.1 Qt的信号与槽（Signals & Slots）与MOC**

Qt最引以为傲的机制是信号与槽，它解耦了对象间的通信。

- **元对象编译器（MOC）**

  ：由于C++缺乏原生的反射机制，Qt必须通过MOC预处理头文件，生成包含元数据的额外C++代码，以支持运行时的方法查找和连接 10。

- **性能代价**

  ：虽然MOC非常强大，但信号槽的调用（特别是跨线程的队列连接）涉及参数的编组（Marshalling）和内存复制，存在不可忽视的运行时开销。此外，MOC增加了构建系统的复杂性，使得Qt项目难以与标准C++构建工具链无缝集成。

#### **3.2.2 Avalonia的编译绑定（Compiled Bindings）**

Avalonia继承了WPF的MVVM（Model-View-ViewModel）模式，但彻底解决了WPF数据绑定的性能痛点。

- **反射的诅咒**

  ：传统的WPF绑定依赖运行时反射（Reflection）来解析属性路径，这在处理包含成千上万个对象的大型列表时，会导致严重的性能下降和内存压力。

- **编译绑定的革新**

  ：Avalonia引入了x:CompileBindings 11。编译器在构建阶段解析XAML中的绑定路径（如{Binding Customer.Name}），并将其直接编译为强类型的C[#代码](javascript:;)。这意味着运行时不再需要反射，绑定的执行速度接近于手写的C[#赋值代码](javascript:;)。

- **类型安全**

  ：与Qt QML（基于JavaScript的动态类型）相比，Avalonia的编译绑定在编译时就能发现类型不匹配错误（例如将字符串绑定到整型属性），极大地提升了大型企业级应用的开发稳定性和可维护性 1。

- **响应式编程（ReactiveUI）**

  ：Avalonia与ReactiveUI深度集成，支持基于Rx.NET的函数响应式编程（FRP）。相比于Qt的命令式信号处理，ReactiveUI允许开发者以声明式的方式组合异步数据流，处理复杂的事件序列（如“防抖”、“节流”、“合并”），这在现代交互密集的应用中具有显著优势 13。

### **3.3 逻辑树与样式系统：CSS化的XAML**

Avalonia对WPF的逻辑树（Logical Tree）和视觉树（Visual Tree）概念进行了扩展，引入了极具创新性的样式选择器系统 15。

- **类CSS选择器**

  ：在Qt中，QSS（Qt Style Sheets）虽然存在，但与QML的样式系统是割裂的。Avalonia允许开发者在XAML中使用类似CSS的选择器，如Style Selector="Button.primary:pointerover TextBlock"。这使得样式的定义与控件结构解耦，设计师可以像写Web页面一样定义全局主题，而无需修改控件代码 17。

- **伪类（Pseudo-classes）**

  ：Avalonia的样式系统支持自定义伪类。开发者可以在C[#代码中触发伪类状态](javascript:;)（如:invalid, :syncing），UI会自动响应样式变化。这种机制比Qt的属性状态绑定更为灵活和解耦。

### **3.4 架构对比总结表**

下面的表格总结了Avalonia UI与Qt Framework在关键技术维度的对比：

| 维度             | Avalonia UI                                | Qt Framework                       | 深度洞察                                                     |
| :--------------- | :----------------------------------------- | :--------------------------------- | :----------------------------------------------------------- |
| **核心语言**     | C# /.NET                                   | C++ / QML (JavaScript-like)        | C[#提供了更高的内存安全性和开发效率](javascript:;)；C++在极端资源受限的嵌入式设备上仍有微弱的性能优势。 |
| **UI描述语言**   | XAML                                       | QML /.ui (XML) / C++               | XAML结合编译绑定提供了静态类型安全 1；QML更灵活但缺乏编译时检查，易在大型项目中引入隐蔽Bug。 |
| **渲染后端**     | Skia (当前), GPU First/Impeller (未来 v12) | QPainter, OpenGL, Vulkan, Metal    | Avalonia的Skia方案保证了跨平台一致性；Qt允许更底层的图形API访问，但在不同OS上的一致性维护成本较高。 |
| **通信机制**     | Compiled Bindings (编译时), ReactiveUI     | Signals & Slots (运行时/MOC)       | 编译绑定消除了反射开销，性能优于基于MOC的动态查找；ReactiveUI在处理异步流时比信号槽更具表现力。 |
| **二进制兼容性** | 极高 (.NET Standard/Core)                  | 低 (需针对每种编译器/平台重新编译) | .NET的中间语言（IL）特性使得Avalonia库的分发（NuGet）远比Qt的C++库链接简单，尤其是在CI/CD流程中。 |
| **样式系统**     | CSS-like XAML Styles                       | QSS / QML Styling                  | Avalonia将CSS的灵活性带入了原生桌面开发，样式复用性极强。    |

##  

## **四 商业决策与创业故事：开源与生存的辩证法**

Mike James领导下的AvaloniaUI OÜ并非典型的硅谷创业故事。它没有巨额融资，没有烧钱扩张，而是走出了一条“技术变现反哺开源”的务实路线。

### **4.1 拒绝外部资本：保持独立的代价与收益**

在Avalonia初创期，团队面临着开源项目普遍的困境：核心开发者需要养家糊口，单纯依靠捐赠无法维持全职开发。尽管有投资人抛出橄榄枝，Mike James及其团队最终决定不接受外部风险投资 5。

- **决策逻辑**

  ：引入VC意味着必须追求高增长和退出机制（IPO或被收购），这往往会导致公司在后期对开源社区进行“收割”（如修改开源协议）。Mike James希望Avalonia保持MIT协议的纯粹性，确立了“可持续开源”（Sustainable Open Source）的愿景。

- **自造血模式**

  ：公司通过提供企业级支持协议（SLA）和定制开发服务（Development Services）赚取了第一桶金 18。许多从WPF迁移到.NET Core的企业需要专家指导，Avalonia团队利用这一需求实现了收支平衡。

### **4.2 从服务到产品：商业模式的进化**

单纯依靠卖“人时”的服务模式难以规模化（Scaling）。Mike James敏锐地捕捉到了市场中的“棕地”（Brownfield）机会——那些不仅需要新开发，更需要维护和迁移旧系统的企业。

- **Avalonia XPF的诞生**

  ：这是公司最关键的战略转折点。团队意识到，虽然Avalonia UI很强大，但重写数百万行WPF代码对许多企业来说是不可能的。于是，他们开发了**Avalonia XPF**——一个与WPF二进制兼容的商业产品 19。

- **商业逻辑**

  ：XPF是闭源且收费的。它利用了Avalonia的跨平台渲染引擎，但在上层完全模拟了WPF的API。企业无需重写代码，甚至无需重新编译部分依赖，就能将WPF应用运行在Linux和macOS上。这种“降维打击”不仅解决了企业的历史遗留问题，也为AvaloniaUI OÜ提供了高利润率的标准化产品收入，从而有资金雇佣更多的核心开发者维护开源版Avalonia UI。

### **4.3 战略融资：Devolutions的300万赞助**

2025年，Avalonia宣布获得Devolutions提供的300万美元赞助 21。

- **非股权融资**

  ：这笔交易的关键在于它不是股权投资，而是“赞助”（Sponsorship）。Devolutions作为Avalonia的重度用户（其远程桌面管理软件基于Avalonia构建），需要确保这个框架的长期稳定和演进。

- **资金用途**

  ：这笔资金被指定用于改进文档、开发工具（Accelerate）以及提升核心框架的稳定性。对于Mike James来说，这是对其“不卖身”策略的最大肯定——通过证明技术的不可替代性，让生态中的受益者自愿为基础设施买单。

##  

## **五 战略级产品：Avalonia XPF的技术护城河**

Avalonia XPF不仅仅是一个商业产品，它代表了Avalonia在技术架构上的极致灵活性，也是其区别于Qt、Flutter等竞争对手的最大护城河。

### **5.1 二进制兼容的魔法：如何实现WPF的跨平台**

WPF长期以来被认为是Windows独占的，因为它深度依赖DirectX和User32.dll。Avalonia XPF打破了这一神话。

- **架构替换**

  ：XPF通过替换WPF底层的PresentationCore和MilCore（WPF的渲染核心），将WPF的绘图指令重定向到Avalonia的Skia渲染后端 20。这意味着上层的WPF控件（Button, DataGrid等）并不“知道”自己运行在非Windows环境下。

- **第三方控件支持**

  ：这是XPF最恐怖的杀手锏。由于保持了API和二进制兼容性，XPF能够运行DevExpress、Telerik、Infragistics等第三方厂商的WPF控件库 20。这些控件库是企业级开发的基石，Qt完全无法触及这一领域（因为这些库是基于.NET的）。XPF使得Avalonia能够直接继承WPF二十年积累的庞大生态资产。

### **5.2 市场定位：收割WPF的存量市场**

Mike James将目光锁定在“WPF遗留资产迁移”这一特定的市场切片上。

- **典型客户**

  ：如施耐德电气、特斯拉、波音等公司，拥有海量的内部WPF工具。随着云计算和DevOps的普及，这些公司迫切需要将这些工具迁移到Linux容器中，或者让研发人员在macOS上使用这些工具。

- **成本对比**

  ：重写一个中型WPF应用可能需要数年时间和数百万美元。而购买XPF许可证并进行简单的适配（主要处理P/Invoke调用），成本仅为重写的几十分之一 26。这种巨大的ROI（投资回报率）使得XPF在企业市场极具吸引力。

### **5.3 许可模式的演变与争议**

XPF的推出并非一帆风顺，其许可模式经历了多次调整。

- **初期困惑**

  ：最初XPF采用“按应用、按平台”收费，导致计算复杂，且对于小微企业价格过高，引发了社区关于“Avalonia变得不再自由”的担忧 27。

- **简化与妥协**

  ：响应社区反馈，Mike James调整了策略，推出了包含所有桌面平台的单一许可证，并承诺核心框架永远开源 29。这种快速的市场响应机制体现了Avalonia作为创业公司的灵活性。

## **六 生态扩张：从桌面霸主到嵌入式新贵**

Avalonia UI的影响力已经超越了单纯的桌面开发，正在向嵌入式、Web及移动端全域渗透。

### **6.1 企业级桌面的统治力：JetBrains的背书**

在桌面端，Avalonia获得了一个里程碑式的胜利：JetBrains的背书。

- **IDE的UI基石**

  ：JetBrains在其著名的.NET性能分析工具dotMemory和dotTrace中使用了Avalonia 30。甚至其旗舰IDE Rider的某些各种跨平台视图也是基于Avalonia构建的。

- **象征意义**

  ：JetBrains作为全球最顶级的开发工具厂商，对UI框架的性能、稳定性和可扩展性有着极其苛刻的要求。他们的选择向全球开发者传递了一个强烈信号——Avalonia已经具备了支撑世界级复杂应用的能力。

### **6.2 嵌入式Linux的突围：挑战Qt的腹地**

嵌入式Linux一直是Qt的传统腹地，但Avalonia正在撕开缺口。

- **DRM (Direct Rendering Manager) 支持**

  ：Avalonia支持Linux DRM模式，这意味着它可以不依赖X11或Wayland桌面环境，直接在帧缓冲（Framebuffer）上渲染UI 32。

- **施耐德电气案例**

  ：施耐德选择Avalonia将其Harmony系列HMI（人机界面）迁移到Linux。原因在于C[#的高开发效率结合Avalonia的高性能渲染](javascript:;)，使得在树莓派级别的硬件上也能实现流畅的、类似智能手机的触摸体验，且无需支付Qt高昂的运行时版税（Runtime Royalties）33。

- **性能优化**

  ：针对低功耗设备，Avalonia进行了大量优化，包括剪裁未使用的代码（Trimming）以减小包体积 34，以及通过SIMD指令集加速Skia的绘图操作。

### **6.3 移动端与Web的追赶：2025年的现状**

相比于桌面端的强势，Avalonia在移动端（iOS/Android）和Web（WebAssembly）仍处于追赶阶段 1。

- **移动端**

  ：v11版本带来了对iOS和Android的正式支持。Avalonia的优势在于“真·跨平台”——UI代码复用率可达99%，且外观完全一致。但在调用原生API（如摄像头、传感器）的便利性上，生态丰富度暂不如MAUI或React Native。

- **WebAssembly (WASM)**

  ：Avalonia支持将整个应用编译为WASM运行在浏览器中。虽然实现了“一套代码覆盖Web”，但WASM的加载体积（通常几十MB）和首屏启动速度仍是挑战。Google Play在2025年提出的16KB页面对齐要求等新标准，也迫使Avalonia团队持续优化底层内存布局 35。Qt也在WASM上发力 36，二者在Web端的竞争更多是关于二进制体积和启动速度的硬核比拼。

## **七 社区博弈与未来挑战：v12、Accelerate与开源的可持续性**

### **7.1 Avalonia Accelerate：生产力工具的商业化**

为了进一步增强盈利能力并补齐生态短板，Avalonia推出了**Accelerate**套件 37。

- **功能矩阵**

  ：包含高级的可视化设计器、WebView控件、高性能媒体播放器以及树形数据网格（TreeDataGrid）的增强版。

- **商业与开源的边界**

  ：这是一个敏感的领域。TreeDataGrid等组件原本是开源的，后来被归入Accelerate（尽管仍有免费版或许可变更），这在社区引发了关于“功能剥离”的争议 27。Mike James需要小心翼翼地平衡付费用户的特权与开源用户的权益，避免出现类似Redis或Elasticsearch的许可协议危机。

### **7.2 技术路线图：v12与未来十年**

面向2025年及以后，Avalonia的技术演进方向非常清晰：

- **GPU优先渲染（Impeller-like）**

  ：v12将引入新的渲染架构，旨在彻底解决Skia在极端场景下的性能瓶颈，利用GPU计算能力处理2D路径，为高刷新率屏幕提供极致流畅体验 7。

- **移动端成熟化**

  ：继续完善Android和iOS的平台集成，目标是让Avalonia成为.NET开发者构建“超级App”的首选，无论是在桌面还是移动端。

### **7.3 结论：后发者的逆袭逻辑**

回顾Mike James从Qt用户到Avalonia创造者的历程，我们看到的是一个经典的“后发者逆袭”故事。Avalonia没有Qt三十年的历史包袱，也没有微软MAUI那样的内部政治斗争。它利用了.NET Core的跨平台红利，结合了Skia的渲染优势，并通过XPF这一神来之笔解决了商业化难题。

在2025年的技术版图中，Avalonia已不再仅仅是WPF的替代品，而是一个具备独立哲学、强大生态和自我造血能力的顶级UI框架。它向行业证明：在巨头林立的时代，一个由社区驱动、商业闭环的开源项目，依然能够凭借技术创新和精准的市场定位，开辟出一片属于自己的广阔天地。

**数据来源说明**：本文基于截至2025年10月的公开资料、技术文档、博客文章及社区讨论整理而成。所有引用均已在文中标识。

创新敢破行惯

，赞3

#### **引用的著作**

1. Episode 120 - Inside Avalonia's Cross-Platform UI Toolkit and the Quest for Quality Documentation with Mike James - The Modern .NET Show, 访问时间为 十二月 10, 2025， https://dotnetcore.show/episode-120-inside-avalonias-cross-platform-ui-toolkit-and-the-quest-for-quality-documentation-with-mike-james/
2. Avalonia (software framework) - Wikipedia, 访问时间为 十二月 10, 2025， https://en.wikipedia.org/wiki/Avalonia_(software_framework)
3. About Avalonia UI, 访问时间为 十二月 10, 2025， https://avaloniaui.net/about
4. 10 years of Avalonia!, 访问时间为 十二月 10, 2025， https://avaloniaui.net/blog/10-years-of-avalonia
5. Supporting Avalonia UI Growth - Seeking constructive feedback and suggestions [#11279](javascript:;), 访问时间为 十二月 10, 2025， https://github.com/AvaloniaUI/Avalonia/discussions/11279
6. Performance of QPainter? - Qt Forum, 访问时间为 十二月 10, 2025， https://forum.qt.io/topic/160428/performance-of-qpainter
7. The Future of Avalonia's Rendering, 访问时间为 十二月 10, 2025， https://avaloniaui.net/blog/the-future-of-avalonia-s-rendering
8. Avalonia vs .NET MAUI: the pragmatic choice for fast, unified .NET apps, 访问时间为 十二月 10, 2025， https://avaloniaui.net/maui-compare
9. Avalonia Partnering with Google's Flutter Team to Bring Impeller Rendering to .NET, 访问时间为 十二月 10, 2025， https://avaloniaui.net/blog/avalonia-partners-with-google-s-flutter-t-eam-to-bring-impeller-rendering-to-net
10. Does large use of signals and slots affect application performance? - Stack Overflow, 访问时间为 十二月 10, 2025， https://stackoverflow.com/questions/10838013/does-large-use-of-signals-and-slots-affect-application-performance
11. Improving Performance | Avalonia Docs, 访问时间为 十二月 10, 2025， https://docs.avaloniaui.net/docs/guides/development-guides/improving-performance
12. Compiled Bindings - Avalonia Docs, 访问时间为 十二月 10, 2025， https://docs.avaloniaui.net/docs/basics/data/data-binding/compiled-bindings
13. ReactiveUI not a cup of tea worth drinking? Anything else with bad taste to never touch (again)? : r/AvaloniaUI - Reddit, 访问时间为 十二月 10, 2025， https://www.reddit.com/r/AvaloniaUI/comments/1h78mc1/reactiveui_not_a_cup_of_tea_worth_drinking/
14. greater/deeper use of Rx & observables · AvaloniaUI Avalonia · Discussion [#6312](javascript:;) - GitHub, 访问时间为 十二月 10, 2025， https://github.com/AvaloniaUI/Avalonia/discussions/6312
15. Control Trees - Avalonia Docs, 访问时间为 十二月 10, 2025， https://docs.avaloniaui.net/docs/concepts/control-trees
16. UI Composition | Avalonia Docs, 访问时间为 十二月 10, 2025， https://docs.avaloniaui.net/docs/concepts/ui-composition
17. What are your experiences with the various UI frameworks? : r/csharp - Reddit, 访问时间为 十二月 10, 2025， https://www.reddit.com/r/csharp/comments/1cnxwpg/what_are_your_experiences_with_the_various_ui/
18. How we make money - Avalonia UI, 访问时间为 十二月 10, 2025， https://avaloniaui.net/handbook/how-we-make-money
19. AvaloniaUI/Avalonia: Develop Desktop, Embedded, Mobile and WebAssembly apps with C# and XAML. The most popular .NET UI client technology - GitHub, 访问时间为 十二月 10, 2025， https://github.com/AvaloniaUI/Avalonia
20. Avalonia XPF - Take your WPF apps cross-platform., 访问时间为 十二月 10, 2025， https://avaloniaui.net/xpf
21. Avalonia Accelerate Backed by $3 Million Deal from Devolutions - Press release - Devolutions, 访问时间为 十二月 10, 2025， https://devolutions.net/company/press-release/avalonia-accelerate-backed-by-3-million-deal-from-devolutions/
22. Three-Year Sponsorship Accelerates Avalonia's Open-Source Roadmap, 访问时间为 十二月 10, 2025， https://avaloniaui.net/blog/three-year-sponsorship-accelerates-avalonia-s-open-source-roadmap
23. .NET MAUI is Coming to Linux and the Browser, Powered by Avalonia - Avalonia UI, 访问时间为 十二月 10, 2025， https://avaloniaui.net/blog/net-maui-is-coming-to-linux-and-the-browser-powered-by-avalonia
24. Welcome | Avalonia Docs, 访问时间为 十二月 10, 2025， https://docs.avaloniaui.net/xpf/welcome
25. Introducing Hybrid XPF: Bridging Avalonia and WPF Controls, 访问时间为 十二月 10, 2025， https://avaloniaui.net/blog/introducing-hybrid-xpf-bridging-avalonia-and-wpf-controls
26. Avalonia XPF, 访问时间为 十二月 10, 2025， https://avaloniaui.net/handbook/avalonia-xpf
27. Avalonia is getting less free (as in freedom, and as in price). - Reddit, 访问时间为 十二月 10, 2025， https://www.reddit.com/r/AvaloniaUI/comments/1k1pozw/avalonia_is_getting_less_free_as_in_freedom_and/
28. When will wpf be cross-platform · Issue [#3952](javascript:;) · dotnet/wpf - GitHub, 访问时间为 十二月 10, 2025， https://github.com/dotnet/wpf/issues/3952
29. Avalonia XPF - License Changes - Avalonia UI, 访问时间为 十二月 10, 2025， https://avaloniaui.net/blog/avalonia-xpf-license-changes
30. JetBrains Rider: The only cross-platform IDE for Avalonia, 访问时间为 十二月 10, 2025， https://www.jetbrains.com/lp/rider-avalonia/
31. Is AvaloniaUI good option for multiplatform GUI in 2024? : r/dotnet - Reddit, 访问时间为 十二月 10, 2025， https://www.reddit.com/r/dotnet/comments/1al38a1/is_avaloniaui_good_option_for_multiplatform_gui/
32. What is the difference between Avalonia Linux DRM/embedded Linux/Android? [#17088](javascript:;), 访问时间为 十二月 10, 2025， https://github.com/AvaloniaUI/Avalonia/discussions/17088
33. Unleashing .NET on Embedded Linux - Avalonia UI, 访问时间为 十二月 10, 2025， https://avaloniaui.net/blog/unleashing-net-on-embedded-linux
34. Welcome to the New Era of App Development: Introducing Avalonia v11, 访问时间为 十二月 10, 2025， https://avaloniaui.net/blog/welcome-to-the-new-era-of-app-development-introducing-avalonia-v11
35. Preparing Your Avalonia Apps for Android's 16 KB Page Size Requirement, 访问时间为 十二月 10, 2025， https://avaloniaui.net/blog/preparing-your-avalonia-apps-for-android-s-16-kb-page-size-requirement
36. Qt and WebAssembly Takes Client Development Mainstream to the Web | [#QtWS22](javascript:;), 访问时间为 十二月 10, 2025， https://www.qt.io/resources/videos/qt-and-webassembly-takes-client-development-mainstream-to-the-web
37. Welcome to the Avalonia Accelerate Docs, 访问时间为 十二月 10, 2025， https://docs.avaloniaui.net/accelerate/welcome
38. Avalonia Accelerate, 访问时间为 十二月 10, 2025， https://avaloniaui.net/accelerate
39. Avalonia Accelerate, 访问时间为 十二月 10, 2025， https://avaloniaui.net/handbook/avalonia-accelerate