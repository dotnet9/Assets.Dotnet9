# CodeWF.EventBus

![CodeWF.EventBus](https://img1.dotnet9.com/site/doc/csharp/imgs/codewf-eventbus.svg)

`CodeWF.EventBus` is a lightweight in-process event bus library suitable for module decoupling in WPF, WinForms, Avalonia UI, ASP.NET Core, and console applications. It supports `Command` command dispatching and `Query<T>` query callbacks, making it easy to implement simple CQRS.

## Suitable Scenarios

- Desktop application modules do not want to directly reference each other's services or ViewModels.
- In ASP.NET Core, you want to use a lightweight event bus to decouple controllers, business handlers, and query logic.
- Console, WinForms, WPF, or Avalonia projects lack a unified IOC container but still want to decouple processes using publish/subscribe.
- You want a solution smaller than `MediatR` with fewer framework constraints, while retaining the Command/Query pattern.

## Package Selection

| Package | Use Case |
| --- | --- |
| `CodeWF.EventBus` | No IOC container or manual subscription. |
| `CodeWF.AspNetCore.EventBus` | ASP.NET Core / MS.DI. |
| `CodeWF.DryIoc.EventBus` | DryIoc / Prism. |
| `CodeWF.IOC.EventBus` | Other IOC containers, with callback-based registration and resolution. |

## Core Types

```csharp
public abstract class Command
{
}

public abstract class Query<TResponse> : Command
{
    public abstract TResponse Result { get; set; }
}
```

Example:

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

## Handler Writing

Event handler methods are marked with `[EventHandler]`, can have only one parameter, which must inherit from `Command`. Return types support `void` and `Task`, and methods can be `public`, `private`, or `static`.

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

## Without IOC Container

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

Publishing commands and queries:

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

When an instance object is no longer needed, it is recommended to actively unsubscribe:

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

`AddEventBus()` scans the assembly for `[Event]` classes and registers them in the container; `UseEventBus()` connects the scanned handlers to the event bus.

## Repository

- GitHub：[https://github.com/dotnet9/CodeWF.EventBus](https://github.com/dotnet9/CodeWF.EventBus)
- NuGet：[https://www.nuget.org/packages/CodeWF.EventBus](https://www.nuget.org/packages/CodeWF.EventBus)