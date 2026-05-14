# CodeWF.EventBus.Socket

![CodeWF.EventBus.Socket](https://img1.dotnet9.com/site/doc/csharp/imgs/codewf-eventbus-socket.svg)

`CodeWF.EventBus.Socket` は、C# プロセス間通信のための軽量 TCP イベントバスライブラリです。Socket をベースに、Publish/Subscribe と Query 型のリクエスト/レスポンス機能を提供し、ローカルのマルチプロセスや軽量サービス間で通信できるようにします。RabbitMQ、Kafka、Redis などの外部 MQ インフラを事前に導入する必要はありません。

## 特徴

- `CodeWF.NetWrapper` を利用した TCP Socket 転送。
- Publish/Subscribe によるプロセス間イベント通知をサポート。
- 同一トピックでの Query/Response インタラクションをサポート。
- クエリ要求は `TaskId` で関連付けられ、同一トピックでの並行クエリに対応。
- サードパーティの MQ に依存せず、軽量なイベント配信や IPC シナリオに最適。
- サンプルプロジェクト `src/EventBusDemo` を同梱。

## インストール

```powershell
Install-Package CodeWF.EventBus.Socket
```

.NET CLI を使用する場合：

```bash
dotnet add package CodeWF.EventBus.Socket
```

## クイックスタート

サーバーを起動：

```csharp
using CodeWF.EventBus.Socket;

IEventServer eventServer = new EventServer();
await eventServer.StartAsync("127.0.0.1", 9100);
```

クライアントに接続：

```csharp
using CodeWF.EventBus.Socket;

IEventClient eventClient = new EventClient();
await eventClient.ConnectAsync("127.0.0.1", 9100);
```

イベントを購読してパブリッシュ：

```csharp
eventClient.Subscribe<NewEmailCommand>("event.email.new", command =>
{
    Console.WriteLine($"メールの件名を受信: {command.Subject}");
});

eventClient.Publish("event.email.new", new NewEmailCommand
{
    Subject = "ご利用ありがとうございます",
    Content = "アカウントの準備が完了しました",
    SendTime = DateTime.Now
}, out _);
```

Query/Response：

```csharp
eventClient.Subscribe<EmailQuery>("event.email.query", query =>
{
    var response = new EmailQueryResponse
    {
        Emails = EmailManager.QueryEmail(query.Subject)
    };

    eventClient.Publish("event.email.query", response, out _);
});

var result = await eventClient.QueryAsync<EmailQuery, EmailQueryResponse>(
    "event.email.query",
    new EmailQuery { Subject = "アカウント" },
    3000);
```

## 使用上の注意

このライブラリは、軽量なイベント配信、同一マシン上のプロセス間通信、小規模サービス間の通知に適しています。現在のメッセージはメモリ上にのみ保持され、永続化、再試行キュー、プロセス再起動後の配信保証は提供されません。本番環境で使用する場合は、必要に応じて認証、暗号化、監視、レート制限、再試行戦略を追加してください。

## リポジトリ

- GitHub：[https://github.com/dotnet9/CodeWF.EventBus.Socket](https://github.com/dotnet9/CodeWF.EventBus.Socket)
- NuGet：[https://www.nuget.org/packages/CodeWF.EventBus.Socket](https://www.nuget.org/packages/CodeWF.EventBus.Socket)