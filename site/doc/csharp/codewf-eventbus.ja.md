# CodeWF.EventBus

![CodeWF.EventBus](https://img1.dotnet9.com/site/doc/csharp/imgs/codewf-eventbus.svg)

`CodeWF.EventBus` は、軽量なインプロセスイベントバスライブラリです。WPF、WinForms、Avalonia UI、ASP.NET Core、コンソールアプリケーションでモジュールの疎結合を実現するのに最適です。`Command` ディスパッチや `Query<T>` による結果返却をサポートし、シンプルな CQRS を実現できます。

## 適したシナリオ

- デスクトップアプリケーションのモジュール間で、お互いのサービスや ViewModel を直接参照したくない場合。
- ASP.NET Core で、軽量イベントバスを使ってコントローラー、ビジネスロジック、クエリを分離したい場合。
- 統一された IOC コンテナがないコンソール、WinForms、WPF、Avalonia プロジェクトでも、サブスクライブ/パブリッシュによる疎結合を実現したい場合。
- `MediatR` よりも小さく、フレームワークの制約が少なく、Command/Query の考え方を残したい場合。

## パッケージ選択

| パッケージ | 適用シナリオ |
| --- | --- |
| `CodeWF.EventBus` | IOC コンテナなし、または手動サブスクライブ。 |
| `CodeWF.AspNetCore.EventBus` | ASP.NET Core / MS.DI。 |
| `CodeWF.DryIoc.EventBus` | DryIoc / Prism。 |
| `CodeWF.IOC.EventBus` | その他の IOC コンテナ。コールバック経由で登録と解決機能を連携。 |

## コア型

```csharp
public abstract class Command
{
}

public abstract class Query<TResponse> : Command
{
    public abstract TResponse Result { get; set; }
}
```

例：

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

## ハンドラの記述方法

イベント処理メソッドは `[EventHandler]` 属性でマークします。パラメータは1つで、`Command` を継承している必要があります。戻り値は `void` または `Task` をサポートし、メソッドは `public`、`private`、`static` のいずれでも構いません。

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

## IOC コンテナなし

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

コマンドとクエリの発行：

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

インスタンスが不要になったら、明示的にサブスクライブ解除することを推奨します。

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

`AddEventBus()` は、アセンブリ内の `[Event]` クラスをスキャンしてコンテナに登録します。`UseEventBus()` は、スキャンされたハンドラをイベントバスに接続します。

## リポジトリ

- GitHub：[https://github.com/dotnet9/CodeWF.EventBus](https://github.com/dotnet9/CodeWF.EventBus)
- NuGet：[https://www.nuget.org/packages/CodeWF.EventBus](https://www.nuget.org/packages/CodeWF.EventBus)