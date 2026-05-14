# CodeWF.NetWeaver

![CodeWF.NetWeaver](https://img1.dotnet9.com/site/doc/csharp/imgs/codewf-netweaver.svg)

`CodeWF.NetWeaver` 是網路封包序列化與反序列化的核心程式庫。`CodeWF.NetWrapper` 建構在其之上，提供 TCP/UDP 輔助類別、命令分派，以及檔案傳輸與檔案管理能力。

## 專案組成

| 專案 | 說明 |
| --- | --- |
| `CodeWF.NetWeaver` | 核心封包序列化 / 反序列化程式庫。 |
| `CodeWF.NetWrapper` | 基於 `CodeWF.NetWeaver` 的 TCP/UDP Socket 輔助程式庫。 |
| `SocketTest.Client` | Wrapper 功能示範用戶端。 |
| `SocketTest.Server` | Wrapper 功能示範伺服器端。 |

## 安裝

僅安裝封包序列化核心：

```bash
dotnet add package CodeWF.NetWeaver
```

如果需要 TCP/UDP 命令分派、檔案傳輸或檔案管理能力，再安裝封裝層：

```bash
dotnet add package CodeWF.NetWrapper
```

## 封包模型

每個封包由固定標頭與物件主體兩部分組成：

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

可以透過 `NetHead` 為 DTO 標記協定標頭資訊：

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

## 基礎用法

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

`CodeWF.NetWrapper` 負責 TCP/UDP 通訊，並將原始封包轉換為強型別命令：

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

## 檔案傳輸與檔案管理

`TcpSocketClient` 與 `TcpSocketServer` 提供檔案瀏覽、建立目錄、刪除路徑、上傳、下載等 API。傳輸流程會沿用同一個 `TaskId`，便於把請求、回應、分塊資料、確認、拒絕和完成訊息關聯起來。

重要行為：

- 檔案區塊大小為 64 KB。
- 上傳與下載都支援基於偏移量的斷點續傳。
- 檔案完成後執行 SHA-256 校驗。
- 設定 `FileSaveDirectory` 後，伺服器端會限制檔案管理操作只能發生在託管根目錄內。

## 倉庫

- GitHub：[https://github.com/dotnet9/CodeWF.NetWeaver](https://github.com/dotnet9/CodeWF.NetWeaver)
- NuGet：[https://www.nuget.org/packages/CodeWF.NetWeaver](https://www.nuget.org/packages/CodeWF.NetWeaver)