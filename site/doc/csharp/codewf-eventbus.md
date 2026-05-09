# CodeWF.EventBus

![CodeWF.EventBus](https://img1.dotnet9.com/site/doc/csharp/imgs/codewf-eventbus.svg)

`CodeWF.EventBus` 是一个轻量的进程内事件总线库，适合在 WPF、WinForms、Avalonia UI、ASP.NET Core 和控制台程序中做模块解耦。它支持 `Command` 命令分发，也支持 `Query<T>` 查询回传，便于实现简单 CQRS。

## 适合场景

- 桌面应用模块之间不想直接引用彼此的服务或 ViewModel。
- ASP.NET Core 中希望用轻量事件总线拆分控制器、业务处理器和查询逻辑。
- 控制台、WinForms、WPF 或 Avalonia 项目没有统一 IOC 容器，但仍想用订阅发布解耦流程。
- 希望比 `MediatR` 更小、更少框架约束，同时保留 Command/Query 思路。

## 包选择

| 包 | 适用场景 |
| --- | --- |
| `CodeWF.EventBus` | 无 IOC 容器或手动订阅。 |
| `CodeWF.AspNetCore.EventBus` | ASP.NET Core / MS.DI。 |
| `CodeWF.DryIoc.EventBus` | DryIoc / Prism。 |
| `CodeWF.IOC.EventBus` | 其他 IOC 容器，通过回调接入注册和解析能力。 |

## 核心类型

```csharp
public abstract class Command
{
}

public abstract class Query<TResponse> : Command
{
    public abstract TResponse Result { get; set; }
}
```

示例：

```csharp
public sealed class CreateProductCommand : Command
{
    public string Name { get; set; } = string.Empty;
    public decimal Price { get; set; }
}

public sealed class ProductQuery : Query<ProductItemDto?>
{
    public Guid ProductId { get; set; }
    public override ProductItemDto? Result { get; set; }
}
```

## 处理器写法

事件处理方法使用 `[EventHandler]` 标记，参数只能有一个，且必须继承自 `Command`。返回值支持 `void` 和 `Task`，方法可以是 `public`、`private` 或 `static`。

```csharp
[Event]
public sealed class ProductEventHandler
{
    [EventHandler]
    private async Task HandleCreateAsync(CreateProductCommand command)
    {
        await Task.CompletedTask;
    }

    [EventHandler(Order = 1)]
    private void HandleQuery(ProductQuery query)
    {
        query.Result = new ProductItemDto
        {
            Id = query.ProductId,
            Name = "Demo",
            Price = 99
        };
    }
}
```

## 无 IOC 容器

```csharp
public sealed class MainViewModel
{
    private readonly IEventBus _eventBus = EventBus.Default;

    public MainViewModel()
    {
        _eventBus.Subscribe(this);
    }

    [EventHandler]
    private void Handle(UpdateTimeCommand command)
    {
        Console.WriteLine(command.Time);
    }
}
```

发布命令和查询：

```csharp
await EventBus.Default.PublishAsync(new CreateProductCommand
{
    Name = "XiaoMi",
    Price = 8999
});

var product = await EventBus.Default.QueryAsync(new ProductQuery
{
    ProductId = Guid.NewGuid()
});
```

实例对象不再使用时，建议主动取消订阅：

```csharp
EventBus.Default.Unsubscribe(this);
```

## ASP.NET Core

```csharp
using CodeWF.AspNetCore.EventBus;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();
builder.Services.AddEventBus();

var app = builder.Build();

app.MapControllers();
app.UseEventBus();

app.Run();
```

`AddEventBus()` 会扫描程序集中的 `[Event]` 类并注册到容器中，`UseEventBus()` 会把扫描得到的处理器接入事件总线。

## 仓库

- GitHub：[https://github.com/dotnet9/CodeWF.EventBus](https://github.com/dotnet9/CodeWF.EventBus)
- NuGet：[https://www.nuget.org/packages/CodeWF.EventBus](https://www.nuget.org/packages/CodeWF.EventBus)
