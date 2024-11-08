---
title: 基于XAML框架和跨平台项目架构设计的深入技术分析
slug: deep-technical-analysis-of-xaml-framework-and-cross-platform-project-architecture
description: 我们深入探讨了基于XAML的各种平台、跨平台战略以及为有效的项目架构设计所需的核心技术。
date: 2024-11-08 21:37:02
lastmod: 2024-11-08 22:03:27
copyright: Reprinted
banner: false
author: Vicky&James
originalTitle: 基于XAML框架和跨平台项目架构设计的深入技术分析
originalLink: https://jamesnet.dev/article/38/chinese
draft: false
cover: https://img1.dotnet9.com/2024/11/cover_03.png
categories: 
    - .NET
tags: 
    - WPF
    - 跨平台
---

本文基于Vicky&James 2024年10月22日在韩国Microsoft总部BMW meetup会议上的演讲内容重新整理而成。这次研讨会我们深入探讨了基于XAML的各种平台、跨平台战略以及为有效的项目架构设计所需的核心技术。

## 介绍

大家好,我们中韩Microsoft MVP夫妇Vicky&James。我们从WPF开始就对包括Uno Platform在内的基于XAML的框架和项目架构设计有着深入的兴趣和经验。大家可以在我们的GitHub仓库中查看和下载各种项目的源代码:[GitHub - jamesnetgroup](https://github.com/jamesnetgroup "GitHub - jamesnetgroup")

![](https://img1.dotnet9.com/2024/11/cover_03.png)

### 目录

1. XAML平台和跨平台概述
2. 考虑跨平台的.NET版本选择策略
3. View和ViewModel的连接策略分析
4. 框架设计的必要功能及实现方案
5. 在其他平台上有效使用WPF技术的核心策略
6. 分布式项目管理的Bootstrapper设计方法论
7. 在桌面跨平台中最大化利用WPF技术的策略

---

### 1. XAML平台和跨平台概述

XAML是一种用于以声明方式定义UI的标记语言,被用于多个平台。XAML具有由对象(即类)组成的层次结构,使开发人员能够以面向对象的方式设计和管理UI。由于这种结构,开发人员直接处理XAML是很自然的。

当WPF首次出现时,它强调了开发人员和设计师之间的协作,但实际上XAML领域通常也由开发人员负责。这是因为XAML不仅仅是简单的设计,而是形成了基于对象的层次结构,在复杂的自定义控件实现中也发挥着重要作用。这种面向开发人员的设计方法促使XAML不仅在WPF中,而且在随后出现的许多平台中都成为核心组件。

特别是,WPF对所有基于XAML的平台都产生了重大影响,并成为这些平台最重要的参考。

#### 1.1 主要的XAML框架

- **WPF**: 用于Windows桌面应用程序开发的强大框架,提供丰富的UI和图形功能。
- **Silverlight**: 用于在web浏览器中运行的互联网应用程序的平台,目前已停止支持。它是WPF的轻量级版本,以插件方式运行。由于Web标准政策的变化,基于插件的Web平台消失,在Silverlight 2中引入了**VisualStateManager(VSM)**来弥补Trigger的缺点。
- **Xamarin.Forms**: 支持iOS、Android和Windows的移动应用开发平台。作为基于Mono的第一个基于XAML的跨平台,被Microsoft战略性收购并成为.NET Core的基础。
- **UWP (Universal Windows Platform)**: 用于开发运行在Windows 10及以上版本的应用程序的平台。需要注册Microsoft Store,并有WinAPI使用限制等约束。支持与WPF相同的自定义控件设计。
- **WinUI 3**: Windows原生UI框架,是最新Windows应用开发的下一代UI平台。继承了UWP的所有优点,同时解决了其限制,并采纳了WPF的可扩展性。
- **MAUI (.NET Multi-platform App UI)**: 从.NET 6开始引入的跨平台UI框架,可以在单一项目中开发移动和桌面应用。
- **Uno Platform**: 允许在各种平台上使用UWP和WinUI的API的框架,支持Web(WebAssembly)、移动和桌面。支持几乎所有平台,并提供与WPF相同的自定义控件设计。
- **Avalonia UI**: 允许在跨平台上使用WPF风格XAML的开源UI框架。支持与WPF相同的自定义控件设计,通过独特的技术扩展支持各种平台。
- **OpenSilver**: 为将旧的Silverlight迁移到OpenSilver而优化的开源平台。以与Silverlight几乎相同的方式运行,也为WPF开发人员提供熟悉的环境。

### 2. 考虑跨平台的.NET版本选择策略

在跨平台应用程序开发中,需要谨慎选择要使用的.NET版本。因为这将直接影响兼容性、功能性和目标平台支持。

#### 2.1 .NET版本选项

- **.NET Framework**: 仅用于Windows,主要用于现有的WPF和WinForms应用程序。
- **.NET Standard 2.0 / 2.1**: 为提供各种.NET实现之间的兼容性而设计的标准。
- **.NET (Core) 3.0及以上**: 支持Windows、macOS、Linux的跨平台.NET实现,包含最新功能和性能改进。

#### 2.2 选择标准和考虑因素

如果需要跨平台支持,应该选择`.NET Core`或最新的`.NET`。如果与现有.NET Framework库或包的兼容性很重要,那么则应该使用`.NET Standard 2.0`。如果想要最新功能和性能改进,就需要考虑`.NET 5及以上`版本。

此外,跨平台框架从.NET 5.0开始考虑兼容性,并且基于最新版本持续进行功能改进,因此建议大家选择最新的.NET版本。

**战略建议**:

- 将通用库编写为`.NET Standard 2.0`以确保最大兼容性。
- 为每个平台创建项目并引用通用库。
- 如果可能,使用`.NET 6及以上`版本以获得最新功能和性能改进。

### 3. View和ViewModel的连接策略分析

在MVVM(Model-View-ViewModel)模式中,View和ViewModel的连接是核心部分。连接方式的不同会导致使用MVVM的方式完全不同。因此,我们需要根据使用MVVM的目的来决定DataContext分配方式。

#### 3.1 传统的DataContext直接分配方式

在代码后台创建ViewModel并直接分配给View的DataContext。

```csharp
public MainWindow()
{
    InitializeComponent();
    DataContext = new MainViewModel();
}
```

**优点**:
- 实现简单直观
- 可以明确控制视图模型的创建时机
- 可以向构造函数传递所需参数

**缺点**:
- View和ViewModel之间产生强耦合
- 单元测试时难以模拟(Mock)ViewModel
- 难以利用依赖注入(DI)
- 需要直接指定DataContext分配时机,可能难以保持一致性

#### 3.2 在XAML中创建ViewModel实例

在XAML中设置DataContext来实例化ViewModel。

```xml
<Window x:Class="MyApp.MainWindow"
        xmlns:local="clr-namespace:MyApp.ViewModels">
    <Window.DataContext>
        <local:MainViewModel />
    </Window.DataContext>
    <!-- Window content -->
</Window>
```

**优点**:
- XAML的智能感知支持可以减少绑定错误
- 可以在设计器中预览实际的数据绑定
- 明确表达View和ViewModel之间的关系

**缺点**:
- ViewModel创建时难以进行依赖注入
- 复杂的初始化逻辑或参数传递受限
- DataContext分配时机被强制化,降低了灵活性

#### 3.3 ViewModel直接创建及依赖传递

在代码后台创建ViewModel直接创建及依赖传递时直接传递所需的依赖。

```csharp
public MainWindow()
{
    InitializeComponent();
    var dataService = new DataService();
    var loggingService = new LoggingService();
    DataContext = new MainViewModel(dataService, loggingService);
}
```

**优点**:
- 可以在创建ViewModel时明确传递所需的依赖
- 可以实现复杂的初始化逻辑
- 可以根据Runtime灵活创建ViewModel实例

**缺点**:
- View需要了解ViewModel的依赖关系
- 随着依赖增加,代码后台会变得复杂
- View和ViewModel之间的耦合度仍然很高
- 需要直接指定DataContext分配时机,可能难以保持一致性
- 由于不使用DI管理项目,可能会出现依赖关系混乱的情况

#### 3.4 活用依赖注入(DI)容器

使用DI容器来管理ViewModel及其依赖可以降低View和ViewModel之间的耦合度。

```csharp
public MainWindow()
{
    InitializeComponent();
    DataContext = ServiceProvider.GetService<MainViewModel>();
}
```

**优点**:
- 降低View和ViewModel之间的耦合度
- 依赖关系集中管理,提高可维护性
- 便于进行单元测试
- 可以在运行时灵活更改依赖关系

**缺点**:
- DI容器的初始配置可能较为复杂
- 团队成员需要理解DI模式
- 仍然需要直接在DataContext中创建视图模型,分配时机的一致性可能难以保持
- 需要决定是将视图模型作为单例还是实例来管理,并考虑视图的生命周期。需要制定明确的规则并严格遵守

#### 3.5 View的自动ViewModel创建策略

为了解决上述问题,我们可以考虑在View创建创建的约定时间点，通过依赖注入创建ViewModel并且分配给DataContext。例如，设计一个基于ContentControl的Veiw，自动创建ViewModel就是一个有效的方法。



```csharp
public class UnoView : ContentControl
{
    public UnoView()
    {
        this.Loaded += (s, e) =>
        {
            var viewModelType = ViewModelLocator.GetViewModelType(this.GetType());
            DataContext = ServiceProvider.GetService(viewModelType);
        };
    }
}
```

**优点**:
- 可以保持DataContext分配时机的一致性
- 降低View和ViewModel之间的耦合度
- ViewModel的创建和依赖注入自动处理
- View不需要知道自己应该使用哪个ViewModel

这几乎是一个没有缺点的方法,通过管理单一的View,可以统一处理时机和处理逻辑。在结构性完善和扩展方面也能保证很好的设计。

不过需要View和ViewModel之间的Mapping,可以使用Dictionary或Mapping Table 来实现。这样可以集中管理View和ViewModel之间的连接信息。关于连接管理的映射方法,我们将在后面的`Bootstrapper设计方法论`中详细讨论。

### 4. 框架设计的必要功能及实现方案

在设计应用程序架构时,构建考虑`可重用性和可扩展性`的框架非常重要。为此,使用依赖注入(DI)容器是必不可少的。

#### 4.1 依赖注入(DI)容器的使用

DI是现代软件开发中不可或缺的模式,对依赖关系管理和降低耦合度有很大帮助。然而,在像WPF这样的桌面应用程序中,Web应用程序中常用的`Microsoft.Extensions.DependencyInjection`等DI容器可能并不完全适合。

##### 4.1.1 Microsoft.Extensions.DependencyInjection的使用和注意事项

`Microsoft.Extensions.DependencyInjection`是.NET官方提供的DI容器,可以说是.NET Foundation的标准。它被用于ASP.NET Core、EntityFrameworkCore、MAUI等各种平台和框架中的几乎所有系统中使用,并提供`Transient`、`Scoped`、 `Singleton`等生命周期管理功能。

然而，在WPF中,这种标准DI的生命周期可能和WPF实际情况并不完全匹配。

**注意事项**:
- 在WPF等桌面应用程序中,可能不需要`Scoped`生命周期
- `Transient`或`Singleton`的概念是为服务或Web应用程序设计的,在WPF中某些功能可能不适用
- 可能带来不必要的复杂性,对于WPF的使用场景来说,更简单轻量的DI容器可能更合适

当然,即使不使用`Transient`这样的生命周期也可以使用DI,但准确理解这些要点是非常重要的。

##### 4.1.2 CommunityToolkit.Mvvm的DI

`CommunityToolkit.Mvvm`并不直接提供像`Microsoft.Extensions.DependencyInjection`这样的DI。这可能是因为`Microsoft.Extensions.DependencyInjection`和WPF的生命周期特性不完全匹配。

但是,`CommunityToolkit.Mvvm`通过提供`Ioc.Default`允许开发者使用任何想要的DI容器。任何实现了`System.IServiceProvider`接口的DI容器都可以注册使用。

因此,使用`CommunityToolkit.Mvvm`时可以选择DI。最常用的DI之一无疑是`Microsoft.Extensions.DependencyInjection`,使用`Prism`这样的DI也是非常有效的组合。

##### 4.1.3 直接设计DI容器的优势

基于`IServiceProvider`接口设计DI的方法可以注册到`CommunityToolkit.Mvvm`的`Ioc.Default`中,实现内部功能的连接和兼容。由于`IServiceProvider`只要求实现`GetService`等最基本的功能,因此可以实现非常简单的DI。

**优点**:
- 实现只包含必要功能的简单DI容器,降低项目复杂性
- 可以在内部设计、控制和扩展各种功能
- 可以精确构建整体框架架构和项目设计
- 提供不依赖特定平台的统一DI容器,有利于跨平台开发

**示例代码**:

```csharp
// 基于IServiceProvider的DI容器实现
public class SimpleServiceProvider : IServiceProvider
{
    private readonly Dictionary<Type, Func<object>> _services = new();

    public void AddService<TService>(Func<TService> implementationFactory)
    {
        _services[typeof(TService)] = () => implementationFactory();
    }

    public object GetService(Type serviceType)
    {
        return _services.TryGetValue(serviceType, out var factory) ? factory() : null;
    }
}

// DI容器注册和使用
var serviceProvider = new SimpleServiceProvider();
serviceProvider.AddService<IMainViewModel>(() => new MainViewModel());

Ioc.Default.ConfigureServices(serviceProvider);
```

这样基于`IServiceProvider`接口实现简单的DI容器,就可以注册到`CommunityToolkit.Mvvm`的`Ioc.Default`中,实现内部功能的连接和兼容。如果觉得使用`Microsoft.Extensions.DependencyInjection`、`Prism`等主流DI太繁重,自己直接来实现一个是非常有吸引力的选择。

**注意**:
如果不遵循`IServiceProvider`等`System.ComponentModel`标准,可能会失去和`CommunityToolkit.Mvvm`的`Ioc`兼容性。但是我们可以将`CommunityToolkit.Mvvm`仅作为MVVM相关的模块,创建一个更专业、更统一的、不依赖特定平台或框架的DI容器。这对于创建可以在跨平台等多个XAML平台上通用的框架是非常适合的。

### 5. WPF技术在其他平台上的有效使用策略

要在其他XAML平台上最大限度地利用WPF强大的功能,我们需要了解一些历史背景和核心策略。同时也需要详细了解可以直接使用WPF技术的平台特性。

#### 5.1 平台间的特征和差异理解

- **UWP和WinUI 3的差异**: UWP作为Windows 10的专用平台，由于应用商店注册指南和WinAPI限制等原因，与WPF和WinForms等传统平台的兼容性较差。因此，WinUI 3应运而生，它不仅继承了UWP的所有优点，还改进了其存在的问题，发展成为了一个像WPF一样具有高自由度的平台。


- **Uno Platform桌面版与WinUI 3的一致性**: Uno Platform支持Windows、macOS、Linux的桌面平台完全遵循WinUI 3的方式。因此,WinUI 3直接使用UWP的核心库，而Uno Platform也同样采用WinUI 3的方式，这意味着所有以`Microsoft.*`开头的DLL库都可以共享使用。


理解这些平台间的特征可以让我们认识到`Uno Platform Desktop`是一个非常高效且具有吸引力的平台。因此，在WPF和Uno Platform之间进行技术共享和转换的策略非常有效且高效，因为它们与WinUI 3和UWP都有着紧密的联系。

#### 5.2 充分利用VisualStateManager(VSM)

由于不是所有平台都可以直接使用WPF的Trigger,所以我们就需要一个替代策略。`VisualStateManager(VSM)`在解决这个问题上起着核心作用。

VSM是在Silverlight 2.0中引入的,用来弥补Trigger不足的,对自定义控件和XAML之间的状态处理进行了优化。随后在.NET 4.0中,VSM也被引入到WPF中,WPF的Button、CheckBox、DataGrid、Window等所有CustomControl的内部设计都从Trigger改为了VSM。

**优点**:
- 在不能直接使用Trigger的平台上可以通过VSM实现相同功能
- 可以有效实现UI状态管理和动画
- 可以通过VSM统一不同平台的不同行为

最终,通过集中使用VSM,就可以实现在WPF、Uno Platform Desktop、WinUI 3、UWP等平台上构建相同的XAML和CustomControl,源代码也可以完全共享。

#### 5.3 灵活运用IValueConverter

`IValueConverter`是一个允许在数据绑定时转换值的接口,对于抽象化平台间差异非常有用。

**策略性使用**:
- 可以实现和替代几乎与Trigger相同的功能,编写简单有效的源代码
- 由于每次都需要创建Converter,而且且重用性标准模糊,建议不要过分追求重用性,而是灵活使用
- 即使没有重用性也要直观使用,重要的是通过明确的命名来尽量减少分支,专门化使用

**局限和补充**:
- 仅使用`IValueConverter`是有限的
- `IValueConverter`应用于简单转换,复杂场景的管理会带来负担,这时我们应通过`VSM`解决
- 复杂的状态处理最好使用`VisualStateManager`

总之,`IValueConverter`补充了VSM的不足,对于简单直接的转换工作应该直观灵活使用,不要过分追求重用性。

### 6. 分布式项目管理的Bootstrapper设计方法论

随着应用程序变得复杂和模块化，初始化过程和依赖管理变得越来越重要。`Bootstrapper模式`在集中管理这些初始化逻辑方面非常有用。

虽然所有平台都以Application设计为基础，但由于各平台的特性和方式不同，Application设计各不相同。因此，为了在所有平台上保持相同的开发方式，使用`Bootstrapper结构设计`是非常有效的。

#### 6.1 Bootstrapper的作用和必要性

**Bootstrapper的功能**:

- **依赖注入设置**：初始化DI容器，注册必要的服务、View和ViewModel。
- **管理视图和视图模型的连接**：通过依赖注入注册View，管理View和ViewModel之间的映射。
- **集中化配置管理**：所有配置都在Bootstrapper中管理，使应用程序项目只执行其角色，其余功能实现通过项目分布式和模块化来管理。
- 此外，还可以灵活地扩展集中化管理项目，且不会影响Application。

**优点**:

- 通过分离应用程序的初始化逻辑来提高代码的可读性和可维护性。
- 通过项目分布式和模块化，可以独立开发功能实现。
- 最小化平台之间的结构差异，保持一致的架构。

#### 6.2 Bootstrapper的设计方案

**示例代码**:
```csharp
namespace Jamesnet.Core;

public abstract class AppBootstrapper
{
    protected readonly IContainer Container;
    protected readonly ILayerManager Layer;
    protected readonly IViewModelMapper ViewModelMapper;

    protected AppBootstrapper()
    {
        Container = new Container();
        Layer = new LayerManager();
        ViewModelMapper = new ViewModelMapper();
        ContainerProvider.SetContainer(Container);
        ConfigureContainer();
    }

    protected virtual void ConfigureContainer()
    {
        Container.RegisterInstance<IContainer>(Container);
        Container.RegisterInstance<ILayerManager>(Layer);
        Container.RegisterInstance<IViewModelMapper>(ViewModelMapper);
        Container.RegisterSingleton<IViewModelInitializer, DefaultViewModelInitializer>();
    }

    protected abstract void RegisterViewModels();
    protected abstract void RegisterDependencies();

    public void Run()
    {
        RegisterViewModels();
        RegisterDependencies();
        OnStartup();
    }

    protected abstract void OnStartup();
}
```

通过这样的抽象化，可以明确强调管理结构，并通过虚方法控制时间点和顺序。这有助于灵活扩展和完善，并且不影响Application，使其在各种平台上以一致的方式运行。

### 7. 在跨平台桌面环境中最大化WPF技术的策略

通过在其他基于XAML的跨平台框架中最大限度地利用WPF中使用的技术和模式，可以提高开发效率。

#### 7.1 实现可在所有平台上运行的框架

![](https://img1.dotnet9.com/2024/11/0301.png)

Jamesnet.Core是基于`.NET Standard 2.0`的框架，允许在WPF、Uno Platform和WinUI 3中实现相同的项目设计。该框架具有以下特点：

- DI设计：利用基于IServiceProvider的DI容器，可以与CommunityToolkit.Mvvm配合使用。 
- MVVM Bootstrapper：集中管理项目的初始化和依赖注入。 
- View和ViewModel之间的连接管理：通过层管理等方式降低View和ViewModel的耦合度。 
- 在所有基于XAML的平台上统一运行。 
- 直接引用存储库源代码，便于调试、功能实现、扩展和研究。

**优点**:

- 无论使用WPF、Uno Platform还是WinUI 3开发，都可以保持相同的架构。
- 使用`Uno Platform Desktop`可以在macOS和Linux上进行开发和运行。
- 可以使用`JetBrains Rider`构建跨平台开发环境。

#### 7.2 实际实现案例分析

**英雄联盟客户端重构项目**利用Jamesnet.Core框架，在WPF、Uno Platform和WinUI 3等不同平台上使用相同的代码库和架构实现。

![](https://img1.dotnet9.com/2024/11/cover_03.png)

![](https://img1.dotnet9.com/2024/11/0302.png)

![](https://img1.dotnet9.com/2024/11/0303.png)

![](https://img1.dotnet9.com/2024/11/0304.png)

- **WPF版本**: [GitHub - leagueoflegends-wpf](https://github.com/JamesnetGroup/leagueoflegends-wpf "GitHub - leagueoflegends-wpf")
- **Uno Platform版本**: [GitHub - leagueoflegends-uno](https://github.com/JamesnetGroup/leagueoflegends-uno "GitHub - leagueoflegends-uno")
- **WinUI 3版本**: [GitHub - leagueoflegends-winui3](https://github.com/JamesnetGroup/leagueoflegends-winui3 "GitHub - leagueoflegends-winui3")

**战略方法**:

- 通过**Jamesnet.Core框架**保持**项目设计的统一性**。
- 利用**DI容器和Bootstrapper**集中管理视图和视图模型。
- 使用`VisualStateManager(VSM)`替代Trigger，在不同平台上以相同方式管理UI状态。

**成果**:

- **97%以上的代码共享**，最大化了向其他平台扩展的可能性。
- 在各种平台上提供**一致的用户体验和开发方法论**，使技术转换更容易。
- 通过项目分散化、模块化和管理集中化，大大降低了开发和维护成本。
- 通过**CustomControl的模块化开发**提高了重构和扩展性，在GPT、Claude等**AI应用**方面，分散的视图系统也更加有效。

### 结论

WPF技术和模式仍然强大，将其应用于跨平台开发可以提高开发效率和代码重用性。特别是使用**Jamesnet.Core框架**，通过DI容器的集中化管理策略和引入Bootstrapper，有助于降低视图和视图模型之间的耦合度，提高可维护性。

此外，通过积极使用**VisualStateManager**和**IValueConverter**，可以最小化平台之间的差异并保持一致的设计。通过这些策略，可以超越WPF基础，战略性地实现跨平台技术扩展。

特别是，**UWP**、**WinUI 3**和**Uno Platform**之间100%相同地使用XAML相关DLL，因此这些平台之间几乎没有差异。因此，对WPF开发者来说，**使用Uno Platform桌面版非常有效且具有战略意义**。这是因为从WPF转换到Uno可以在几小时内完成，转换到WinUI 3也非常容易。

未来，WPF技术和基于XAML的框架将继续发展，利用这些进行跨平台开发将变得更加重要。开发人员需要很好地把握这些趋势，制定适当的策略，开发高质量的应用程序。

### 参考

#### 主要仓库

- **Jamesnet.Core框架**: [GitHub - jamesnet.core](https://github.com/JamesnetGroup/leagueoflegends-wpf/tree/main/src/Jamesnet.Core "GitHub - jamesnet.core")
  - 在所有基于XAML的平台上运行的框架，提供DI、MVVM、Bootstrapper等功能。
- **英雄联盟客户端重构项目**:
  - **WPF版本**: [GitHub - leagueoflegends-wpf](https://github.com/JamesnetGroup/leagueoflegends-wpf "GitHub - leagueoflegends-wpf")
  - **Uno Platform版本**: [GitHub - leagueoflegends-uno](https://github.com/JamesnetGroup/leagueoflegends-uno "GitHub - leagueoflegends-uno")
  - **WinUI 3版本**: [GitHub - leagueoflegends-winui3](https://github.com/JamesnetGroup/leagueoflegends-winui3 "GitHub - leagueoflegends-winui3")


## 目前已更新的WPF教程（自定义控件）

- [实现主题切换](https://www.bilibili.com/video/BV1FN41eHE7e "实现主题切换")
- [实现Riot PlayButton](https://www.bilibili.com/video/BV1Tu4y1j7Ei "实现Riot PlayButton")
- [实现导航栏](https://www.bilibili.com/video/BV1Ui4y1a717 "实现导航栏")
- [实现Riot Slider](https://www.bilibili.com/video/BV1uy421a7yM "实现Riot Slider")
- [实现智能日期](https://www.bilibili.com/video/BV1pE421L7c2 "实现智能日期")
- [实现Cupertino TreeView](https://www.bilibili.com/video/BV1xz42187wV "实现Cupertino TreeView")

![](https://img1.dotnet9.com/2024/11/030.png)