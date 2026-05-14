# CodeWF.NetWeaver

![CodeWF.NetWeaver](https://img1.dotnet9.com/site/doc/csharp/imgs/codewf-netweaver.svg)

`CodeWF.NetWeaver` is the core library for network packet serialization and deserialization. `CodeWF.NetWrapper` is built on top of it, providing TCP/UDP helper classes, command dispatching, as well as file transfer and file management capabilities.

## Project Structure

| Project | Description |
| --- | --- |
| `CodeWF.NetWeaver` | Core packet serialization/deserialization library. |
| `CodeWF.NetWrapper` | TCP/UDP socket helper library based on `CodeWF.NetWeaver`. |
| `SocketTest.Client` | Wrapper feature demo client. |
| `SocketTest.Server` | Wrapper feature demo server. |

## Installation

Install only the core packet serialization:

```bash
dotnet add package CodeWF.NetWeaver
```

If TCP/UDP command dispatching, file transfer, or file management is needed, install the wrapper layer as well:

```bash
dotnet add package CodeWF.NetWrapper
```

## Packet Model

Each packet consists of a fixed header and an object body:

```text
Header
- BufferLen: int
- SystemId: long
- ObjectId: ushort
- ObjectVersion: byte
- UnixTimeMilliseconds: long

Body
- Serialized object payload
```

You can annotate DTOs with protocol header information using `NetHead`:

```csharp
using CodeWF.NetWeaver.Base;

[NetHead(10, 1)]
public class ResponseProcessList : INetObject
{
    public int TaskId { get; set; }
    public int TotalSize { get; set; }
    public List<ProcessItem>? Processes { get; set; }
}
```

## Basic Usage

Serialization:

```csharp
var netObject = new ResponseProcessList
{
    TaskId = 3,
    TotalSize = 2,
    Processes =
    [
        new ProcessItem { Pid = 1, Name = "CodeWF.NetWeaver" },
        new ProcessItem { Pid = 2, Name = "CodeWF.NetWrapper" }
    ]
};

var buffer = netObject.Serialize(systemId: 32);
```

Deserialization:

```csharp
var deserialized = buffer.Deserialize<ResponseProcessList>();
Console.WriteLine($"Process count: {deserialized.Processes?.Count ?? 0}");
```

## CodeWF.NetWrapper

`CodeWF.NetWrapper` handles TCP/UDP communication and converts raw packets into strongly typed commands:

```csharp
using CodeWF.EventBus;
using CodeWF.NetWrapper.Commands;
using CodeWF.NetWrapper.Helpers;

var server = new TcpSocketServer();
await server.StartAsync("Server", "0.0.0.0", 8888);

EventBus.Default.Subscribe<SocketCommand>(async (sender, command) =>
{
    await Task.CompletedTask;
});
```

## File Transfer and File Management

`TcpSocketClient` and `TcpSocketServer` provide APIs for file browsing, directory creation, path deletion, upload, download, etc. The transfer flow reuses the same `TaskId` to associate request, response, chunk data, acknowledgment, rejection, and completion messages.

Important behaviors:

- File chunk size is 64 KB.
- Both upload and download support offset-based resume.
- SHA-256 verification is performed upon file completion.
- When `FileSaveDirectory` is set, the server restricts file management operations to within the managed root directory.

## Repository

- GitHub: [https://github.com/dotnet9/CodeWF.NetWeaver](https://github.com/dotnet9/CodeWF.NetWeaver)
- NuGet: [https://www.nuget.org/packages/CodeWF.NetWeaver](https://www.nuget.org/packages/CodeWF.NetWeaver)