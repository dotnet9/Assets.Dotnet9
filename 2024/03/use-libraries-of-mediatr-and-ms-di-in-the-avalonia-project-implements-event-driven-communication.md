---
title: 在Avalonia项目中使用MediatR和MS.DI库实现事件驱动通信
slug: use-libraries-of-mediatr-and-ms-di-in-the-avalonia-project-implements-event-driven-communication
description: 在C#开发过程中，我们经常需要处理各种事件，有时候还需要动态地注册第三方库定义的事件。今天，我将为大家分享一个关于如何动态注册第三方库事件的Demo，并根据提供的代码和注释，详细讲解每一步骤。
date: 2024-03-02 09:57:26
lastmod: 2024-03-02 13:45:27
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2024/02/cover_01.png
categories: .NET
tags: AvaloniaUI,MediatR
---

大家好，我是沙漠尽头的狼！

AvaloniaUI是一个强大的跨平台.NET客户端开发框架，让开发者能够针对Windows、Linux、macOS、Android和iOS等多个平台构建应用程序。在构建复杂的应用程序时，模块化和组件间的通信变得尤为重要。Prism框架提供了模块化的开发方式，支持插件的热拔插，而MediatR则是一个实现了中介者（Mediator）模式的事件订阅发布框架，非常适合用于模块之间以及模块与主程序之间的通信。

本文重点是介绍`MediatR`，它 是 `.NET` 中的开源简单中介者模式实现。它通过一种进程内消息传递机制（无其他外部依赖），进行请求/响应、命令、查询、通知和事件的消息传递，并通过泛型来支持消息的智能调度。开源库地址是 https://github.com/jbogard/MediatR。

本文将详细介绍如何在Avalonia项目中使用MediatR和Microsoft的依赖注入（MS.DI）库来实现事件驱动的通信。

![](https://img1.dotnet9.com/2024/03/0103.png)

## 0. 基础知识准备

`MediatR` 的基本用法，`MediatR`中有两种消息传递的方式：

- `Request/Response`，用于一个单独的Handler。
- `Notification`，用于多个Handler。

### Request/Response

`Request/Response` 有点类似于 HTTP 的 Request/Response，发出一个 Request 会得到一个 Response。

`Request` 消息在 MediatR 中，有两种类型：

- `IRequest<T>` 返回一个T类型的值。
- `IRequest` 不返回值。

对于每个 request 类型，都有相应的 handler 接口：

- `IRequestHandler<T, U>` 实现该接口并返回 `Task<U>`
- `RequestHandler<T, U>` 继承该类并返回 `U`
- `IRequestHandler<T>` 实现该接口并返回 `Task<Unit>`
- `AsyncRequestHandler<T>` 继承该类并返回 `Task`
- `RequestHandler<T>` 继承该类不返回

### Notification

`Notification` 就是通知，调用者发出一次，然后可以有多个处理者参与处理。

![](https://img1.dotnet9.com/2024/03/0105.png)

## 1. 准备工作

首先，确保你的Avalonia项目中已经安装了必要的NuGet包。你将需要`Prism.DryIoc.Avalonia`作为依赖注入容器，以及`MediatR`来处理事件的发布和订阅。此外，为了将MediatR集成到DryIoc容器中，你还需要`DryIoc.Microsoft.DependencyInjection`包（这里感谢网友`寒`提供的技术解答）。

在项目的`.csproj`文件或NuGet包管理器中添加以下引用：

```xml
<PackageReference Include="Prism.DryIoc.Avalonia" Version="8.1.97.11072" /> 
<PackageReference Include="MediatR" Version="12.2.0" />  
<PackageReference Include="DryIoc.Microsoft.DependencyInjection" Version="8.0.0-preview-01" />
```

## 2. 配置容器和注册服务

在Avalonia项目中，你需要配置DryIoc容器以使用Microsoft的DI扩展，并注册MediatR服务。这通常在你的主启动类（如`App.axaml.cs`）中完成。

以下是配置容器和注册服务的示例代码：

```csharp
namespace CodeWF.Tools.Desktop;

public class App : PrismApplication
{
    public override void Initialize()
    {
        AvaloniaXamlLoader.Load(this);
        base.Initialize(); // <-- Required
    }

    protected override void ConfigureModuleCatalog(IModuleCatalog moduleCatalog)
    {
        base.ConfigureModuleCatalog(moduleCatalog);

        moduleCatalog.AddModule<SlugifyStringModule>();
        moduleCatalog.AddModule<FooterModule>();
    }

    protected override void ConfigureRegionAdapterMappings(RegionAdapterMappings regionAdapterMappings)
    {
        base.ConfigureRegionAdapterMappings(regionAdapterMappings);
        regionAdapterMappings.RegisterMapping(typeof(StackPanel), Container.Resolve<StackPanelRegionAdapter>());
        regionAdapterMappings.RegisterMapping(typeof(Grid), Container.Resolve<GridRegionAdapter>());
        regionAdapterMappings.RegisterMapping(typeof(TabControl), Container.Resolve<TabControlAdapter>());
    }

    protected override AvaloniaObject CreateShell()
    {
        return Container.Resolve<MainWindow>();
    }

    protected override void RegisterTypes(IContainerRegistry containerRegistry)
    {
        var container = containerRegistry.GetContainer();
        // Views - Generic
        containerRegistry.Register<MainWindow>();

        containerRegistry.RegisterSingleton<INotificationService, NotificationService>();
        containerRegistry.RegisterSingleton<IClipboardService, ClipboardService>();
    }

    /// <summary>
    /// 1、DryIoc.Microsoft.DependencyInjection低版本可不要这个方法（5.1.0及以下）
    /// 2、高版本必须，否则会抛出异常：System.MissingMethodException:“Method not found: 'DryIoc.Rules DryIoc.Rules.WithoutFastExpressionCompiler()'.”
    /// 参考issues：https://github.com/dadhi/DryIoc/issues/529
    /// </summary>
    /// <returns></returns>
    protected override Rules CreateContainerRules()
    {
        return Rules.Default.WithConcreteTypeDynamicRegistrations(reuse: Reuse.Transient)
            .With(Made.Of(FactoryMethod.ConstructorWithResolvableArguments))
            .WithFuncAndLazyWithoutRegistration()
            .WithTrackingDisposableTransients()
            //.WithoutFastExpressionCompiler()
            .WithFactorySelector(Rules.SelectLastRegisteredFactory());
    }

    protected override IContainerExtension CreateContainerExtension()
    {
        IContainer container = new Container(CreateContainerRules());
        container.WithDependencyInjectionAdapter();

        return new DryIocContainerExtension(container);
    }

    protected override void RegisterRequiredTypes(IContainerRegistry containerRegistry)
    {
        base.RegisterRequiredTypes(containerRegistry);

        IServiceCollection services = ConfigureServices();

        IContainer container = ((IContainerExtension<IContainer>)containerRegistry).Instance;

        container.Populate(services);
    }

    private static ServiceCollection ConfigureServices()
    {
        var services = new ServiceCollection();

        // 注入MediatR
        var assemblies = AppDomain.CurrentDomain.GetAssemblies().ToList();

        // 添加模块注入，未显示调用模块类型前，模块程序集是未加载到当前程序域`AppDomain.CurrentDomain`的
        var assembly = typeof(SlugifyStringModule).GetAssembly();
        assemblies.Add(assembly);
        services.AddMediatR(configure =>
        {
            configure.RegisterServicesFromAssemblies(assemblies.ToArray());
        });

        return services;
    }
}
```

在上面的代码中，我们重写了`CreateContainerRules`、`CreateContainerExtension`和`RegisterRequiredTypes`方法以配置DryIoc容器，并注册了MediatR服务和相关处理程序。

注意，在注册MediatR服务时，我们从当前已加载的程序集列表中查找并注册处理程序。如果模块是按需加载的，请确保在注册处理程序之前已加载了相应的模块。

此外，我们还演示了如何手动添加模块程序集到列表中以便注册处理程序。这通常在你需要显式控制哪些模块和处理程序被注册时很有用。但是，请注意，在大多数情况下，你可能希望使用更自动化的方式来加载和注册模块及处理程序（例如，通过扫描特定目录或使用约定等）。这取决于你的具体需求和项目结构。

另外，请注意代码中的注释和说明，它们提供了有关每个步骤和配置的额外信息。在实际项目中，你可能需要根据项目的实际情况和需求进行相应的调整和优化。例如，你可能需要处理循环依赖、配置作用域、使用拦截器或装饰器等高级功能。这些都可以在DryIoc和MediatR的文档中找到更详细的说明和示例。

## 3. MediatR2种传递方式

有了前面的基础知识准备，我们添加类库工程`CodeWF.Tools.MediatR.Notifications`，添加请求定义：

```csharp
public class TestRequest : IRequest<string>
{
    public string? Args { get; set; }
}
```

添加通知定义：

```csharp
public class TestNotification : INotification
{
    public string? Args { get; set; }
}
```

请求和通知定义结构一样，只有一个字符串参数，只是实现接口不同。

## 4. 添加处理程序

![程序结构](https://img1.dotnet9.com/2024/03/0106.png)

在AvaloniaUI主工程(CodeWF.Tools.Desktop)添加请求响应处理程序：

```csharp
public class TestHandler : IRequestHandler<TestRequest, string>
{
    public async Task<string> Handle(TestRequest request, CancellationToken cancellationToken)
    {
        return await Task.FromResult($"主工程处理程序：Args = {request.Args}, Now = {DateTime.Now}");
    }
}
```

添加通知响应处理程序：

```csharp
public class TestNotificationHandler(INotificationService notificationService) : INotificationHandler<TestNotification>
{
    public Task Handle(TestNotification notification, CancellationToken cancellationToken)
    {
        notificationService.Show("Notification",
            $"主工程Notification处理程序：Args = {notification.Args}, Now = {DateTime.Now}");
        return Task.CompletedTask;
    }
}
```

在模块【CodeWF.Tools.Modules.SlugifyString】中添加请求响应处理程序(因为顺序关系，不会触发，这里添加只是演示请求为一对一响应）：

```csharp
public class TestHandler : IRequestHandler<TestRequest, string>
{
    public async Task<string> Handle(TestRequest request, CancellationToken cancellationToken)
    {
        return await Task.FromResult($"模块【SlugifyString】Request处理程序：Args = {request.Args}, Now = {DateTime.Now}");
    }
}
```

添加通知响应处理程序（会和主工程通知响应处理程序一样被触发）：

```csharp
public class TestNotificationHandler(INotificationService notificationService) : INotificationHandler<TestNotification>
{
    public Task Handle(TestNotification notification, CancellationToken cancellationToken)
    {
        notificationService.Show("Notification",
            $"模块【SlugifyString】Notification处理程序：Args = {notification.Args}, Now = {DateTime.Now}");
        return Task.CompletedTask;
    }
}
```

## 5. 请求和通知触发

触发操作我们写在模块【CodeWF.Tools.Modules.SlugifyString】中，在模块的ViewModel类里通过依赖注入获取请求和通知的发送者实例ISender和IPublisher：

```csharp
using Unit = System.Reactive.Unit;

namespace CodeWF.Tools.Modules.SlugifyString.ViewModels;

public class SlugifyViewModel : ViewModelBase
{
    private readonly INotificationService _notificationService;
    private readonly IClipboardService? _clipboardService;
    private readonly ITranslationService? _translationService;

    public SlugifyViewModel(INotificationService notificationService, IClipboardService clipboardService,
        ITranslationService translationService, ISender sender, IPublisher publisher) : base(sender, publisher)
    {
        _notificationService = notificationService;
        _clipboardService = clipboardService;
        _translationService = translationService;
        KindChanged = ReactiveCommand.Create<TranslationKind>(OnKindChanged);
    }


    public async Task ExecuteMediatRRequestAsync()
    {
        var result = Sender.Send(new TestRequest() { Args = To });
        _notificationService.Show("MediatR", $"收到响应：{result.Result}");
    }

    public async Task ExecuteMediatRNotificationAsync()
    {
        await Publisher.Publish(new TestNotification() { Args = To });
    }

}
```

界面按钮触发调用ISender.Send发出请求并得到响应，通过IPublisher.Publish发出通知，程序运行效果：

请求效果：虽然在主工程和模块工程都注册了一个响应，但只有主工程被触发：

![](https://img1.dotnet9.com/2024/03/0101.gif)

通知效果：在主工程和模块工程都注册了一个响应，都被触发：

![](https://img1.dotnet9.com/2024/03/0101.gif)

代码并未贴全，但核心原理就是这样，欢迎留言交流。

## 6. 参考

参考文章：[MediatR 在 .NET 应用中的实践](https://yimingzhi.net/2021/12/mediatr-zai-dotnet-ying-yong-zhong-de-shi-jian)

本文源码：[Github](https://github.com/dotnet9/CodeWF/tree/main/src/CodeWF.Tools.Desktop)