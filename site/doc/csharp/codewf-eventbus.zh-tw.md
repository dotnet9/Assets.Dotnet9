# CodeWF.EventBus

![CodeWF.EventBus](https://img1.dotnet9.com/site/doc/csharp/imgs/codewf-eventbus.svg)

`CodeWF.EventBus` 是一個輕量的進程內事件匯流排庫，適合在 WPF、WinForms、Avalonia UI、ASP.NET Core 和控制台程式中做模組解耦。它支援 `Command` 命令分發，也支援 `Query<T>` 查詢回傳，便於實現簡單 CQRS。

## 適合場景

- 桌面應用模組之間不想直接引用彼此的服務或 ViewModel。
- ASP.NET Core 中希望用輕量事件匯流排拆分控制器、業務處理器和查詢邏輯。
- 控制台、WinForms、WPF 或 Avalonia 專案沒有統一的 IOC 容器，但仍想用訂閱發佈解耦流程。
- 希望比 `MediatR` 更小、更少框架約束，同時保留 Command/Query 思路。

## 套件選擇

| 套件 | 適用場景 |
| --- | --- |
| `CodeWF.EventBus` | 無 IOC 容器或手動訂閱。 |
| `CodeWF.AspNetCore.EventBus` | ASP.NET Core / MS.DI。 |
| `CodeWF.DryIoc.EventBus` | DryIoc / Prism。 |
| `CodeWF.IOC.EventBus` | 其他 IOC 容器，透過回呼接入註冊和解析能力。 |

## 核心類型

```csharp
public abstract class Command
{
}

public abstract class Query<TResponse> : Command
{
    public abstract TResponse Result { get; set; }
}
```

範例：

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

## 處理器寫法

事件處理方法使用 `[EventHandler]` 標記，參數只能有一個，且必須繼承自 `Command`。回傳值支援 `void` 和 `Task`，方法可以是 `public`、`private` 或 `static`。

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

## 無 IOC 容器

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

發佈命令和查詢：

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

實例物件不再使用時，建議主動取消訂閱：

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

`AddEventBus()` 會掃描組件中的 `[Event]` 類別並註冊到容器中，`UseEventBus()` 會把掃描到的處理器接入事件匯流排。

## 倉庫

- GitHub：[https://github.com/dotnet9/CodeWF.EventBus](https://github.com/dotnet9/CodeWF.EventBus)
- NuGet：[https://www.nuget.org/packages/CodeWF.EventBus](https://www.nuget.org/packages/CodeWF.EventBus)