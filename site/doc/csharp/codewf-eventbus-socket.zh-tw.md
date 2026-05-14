# CodeWF.EventBus.Socket

![CodeWF.EventBus.Socket](https://img1.dotnet9.com/site/doc/csharp/imgs/codewf-eventbus-socket.svg)

`CodeWF.EventBus.Socket` 是一個面向 C# 行程間通訊的輕量級 TCP 事件匯流排庫。它基於 Socket 提供發佈訂閱與 Query 式請求回應能力，讓本地多行程或輕量服務之間可以通訊，而不必先引入 RabbitMQ、Kafka、Redis 等外部 MQ 基礎設施。

## 特性

- 基於 `CodeWF.NetWrapper` 的 TCP Socket 傳輸。
- 支援 Publish/Subscribe 跨行程事件通知。
- 支援同一主題下的 Query/Response 互動。
- 查詢請求按 `TaskId` 關聯，支援同主題並行查詢。
- 不依賴第三方 MQ，適合輕量級事件分發和 IPC 場景。
- 內附範例專案 `src/EventBusDemo`。

## 安裝

```powershell
Install-Package CodeWF.EventBus.Socket
```

也可以使用 .NET CLI：

```bash
dotnet add package CodeWF.EventBus.Socket
```

## 快速開始

啟動服務端：

```csharp
using CodeWF.EventBus.Socket;

IEventServer eventServer = new EventServer();
await eventServer.StartAsync("127.0.0.1", 9100);
```

連線用戶端：

```csharp
using CodeWF.EventBus.Socket;

IEventClient eventClient = new EventClient();
await eventClient.ConnectAsync("127.0.0.1", 9100);
```

訂閱並發佈事件：

```csharp
eventClient.Subscribe<NewEmailCommand>("event.email.new", command =>
{
    Console.WriteLine($"收到郵件主題：{command.Subject}");
});

eventClient.Publish("event.email.new", new NewEmailCommand
{
    Subject = "歡迎使用",
    Content = "您的帳號已經準備完成",
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
    new EmailQuery { Subject = "帳戶" },
    3000);
```

## 使用邊界

這個函式庫適合輕量事件分發、本機多行程通訊和小型服務間通知。目前訊息僅保存在記憶體中，不提供持久化、重試佇列或行程重啟後的投遞保證；如果用在生產環境，應按業務需求補充認證、加密、監控、限流和重試策略。

## 倉庫

- GitHub：[https://github.com/dotnet9/CodeWF.EventBus.Socket](https://github.com/dotnet9/CodeWF.EventBus.Socket)
- NuGet：[https://www.nuget.org/packages/CodeWF.EventBus.Socket](https://www.nuget.org/packages/CodeWF.EventBus.Socket)