# CodeWF.NetWeaver

![CodeWF.NetWeaver](https://img1.dotnet9.com/site/doc/csharp/imgs/codewf-netweaver.svg)

`CodeWF.NetWeaver` 是网络数据包序列化与反序列化的核心库。`CodeWF.NetWrapper` 构建在它之上，提供 TCP/UDP 帮助类、命令分发，以及文件传输和文件管理能力。

## 项目组成

| 项目 | 说明 |
| --- | --- |
| `CodeWF.NetWeaver` | 核心数据包序列化 / 反序列化库。 |
| `CodeWF.NetWrapper` | 基于 `CodeWF.NetWeaver` 的 TCP/UDP Socket 帮助库。 |
| `SocketTest.Client` | Wrapper 功能演示客户端。 |
| `SocketTest.Server` | Wrapper 功能演示服务端。 |

## 安装

仅安装数据包序列化核心：

```bash
dotnet add package CodeWF.NetWeaver
```

如果需要 TCP/UDP 命令分发、文件传输或文件管理能力，再安装封装层：

```bash
dotnet add package CodeWF.NetWrapper
```

## 数据包模型

每个数据包由固定头部和对象正文两部分组成：

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

可以通过 `NetHead` 为 DTO 标记协议头信息：

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

## 基础用法

序列化：

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

反序列化：

```csharp
var deserialized = buffer.Deserialize<ResponseProcessList>();
Console.WriteLine($"Process count: {deserialized.Processes?.Count ?? 0}");
```

## CodeWF.NetWrapper

`CodeWF.NetWrapper` 负责 TCP/UDP 通信，并将原始数据包转换为强类型命令：

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

## 文件传输与文件管理

`TcpSocketClient` 和 `TcpSocketServer` 提供文件浏览、创建目录、删除路径、上传、下载等 API。传输流程会沿用同一个 `TaskId`，便于把请求、响应、分块数据、确认、拒绝和完成消息关联起来。

重要行为：

- 文件块大小为 64 KB。
- 上传和下载都支持基于偏移量的断点续传。
- 文件完成后执行 SHA-256 校验。
- 设置 `FileSaveDirectory` 后，服务端会限制文件管理操作只能发生在托管根目录内。

## 仓库

- GitHub：[https://github.com/dotnet9/CodeWF.NetWeaver](https://github.com/dotnet9/CodeWF.NetWeaver)
- NuGet：[https://www.nuget.org/packages/CodeWF.NetWeaver](https://www.nuget.org/packages/CodeWF.NetWeaver)
