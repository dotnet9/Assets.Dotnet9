# CodeWF.EventBus.Socket

![CodeWF.EventBus.Socket](https://img1.dotnet9.com/site/doc/csharp/imgs/codewf-eventbus-socket.svg)

`CodeWF.EventBus.Socket` is a lightweight TCP event bus library for inter-process communication in C#. It is based on Socket and provides publish-subscribe and query-response capabilities, allowing local multi-process or lightweight services to communicate without first introducing external MQ infrastructure like RabbitMQ, Kafka, Redis, etc.

## Features

- TCP Socket transport based on `CodeWF.NetWrapper`.
- Supports Publish/Subscribe cross-process event notification.
- Supports Query/Response interaction under the same topic.
- Query requests are associated by `TaskId`, supporting concurrent queries on the same topic.
- No dependency on third-party MQ, suitable for lightweight event distribution and IPC scenarios.
- Comes with sample project `src/EventBusDemo`.

## Installation

```powershell
Install-Package CodeWF.EventBus.Socket
```

Or use .NET CLI:

```bash
dotnet add package CodeWF.EventBus.Socket
```

## Quick Start

Start the server:

```csharp
using CodeWF.EventBus.Socket;

IEventServer eventServer = new EventServer();
await eventServer.StartAsync("127.0.0.1", 9100);
```

Connect the client:

```csharp
using CodeWF.EventBus.Socket;

IEventClient eventClient = new EventClient();
await eventClient.ConnectAsync("127.0.0.1", 9100);
```

Subscribe and publish events:

```csharp
eventClient.Subscribe<NewEmailCommand>("event.email.new", command =>
{
    Console.WriteLine($"Received email subject: {command.Subject}");
});

eventClient.Publish("event.email.new", new NewEmailCommand
{
    Subject = "Welcome",
    Content = "Your account is ready",
    SendTime = DateTime.Now
}, out _);
```

Query/Response:

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
    new EmailQuery { Subject = "Account" },
    3000);
```

## Usage Boundaries

This library is suitable for lightweight event distribution, local multi-process communication, and notifications between small services. Messages are currently only kept in memory; no persistence, retry queue, or delivery guarantees after process restart are provided. If used in production, authentication, encryption, monitoring, rate limiting, and retry strategies should be added according to business needs.

## Repository

- GitHub: [https://github.com/dotnet9/CodeWF.EventBus.Socket](https://github.com/dotnet9/CodeWF.EventBus.Socket)
- NuGet: [https://www.nuget.org/packages/CodeWF.EventBus.Socket](https://www.nuget.org/packages/CodeWF.EventBus.Socket)