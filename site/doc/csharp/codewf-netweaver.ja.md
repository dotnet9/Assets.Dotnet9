# CodeWF.NetWeaver

![CodeWF.NetWeaver](https://img1.dotnet9.com/site/doc/csharp/imgs/codewf-netweaver.svg)

`CodeWF.NetWeaver` は、ネットワークパケットのシリアル化と逆シリアル化を行うコアライブラリです。`CodeWF.NetWrapper` はその上に構築され、TCP/UDP ヘルパークラス、コマンドディスパッチ、ファイル転送およびファイル管理機能を提供します。

## プロジェクト構成

| プロジェクト | 説明 |
| --- | --- |
| `CodeWF.NetWeaver` | コアパケットシリアル化/逆シリアル化ライブラリ |
| `CodeWF.NetWrapper` | `CodeWF.NetWeaver` に基づく TCP/UDP ソケットヘルパーライブラリ |
| `SocketTest.Client` | Wrapper 機能デモクライアント |
| `SocketTest.Server` | Wrapper 機能デモサーバー |

## インストール

パケットシリアル化コアのみをインストールする場合:

```bash
dotnet add package CodeWF.NetWeaver
```

TCP/UDP コマンドディスパッチ、ファイル転送、ファイル管理機能も必要な場合は、ラッパーレイヤーもインストールします:

```bash
dotnet add package CodeWF.NetWrapper
```

## パケットモデル

各パケットは、固定ヘッダーとオブジェクト本体の2つの部分で構成されます:

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

`NetHead` 属性を使用してDTOにプロトコルヘッダー情報をマークできます:

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

## 基本的な使い方

シリアル化:

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

逆シリアル化:

```csharp
var deserialized = buffer.Deserialize<ResponseProcessList>();
Console.WriteLine($"Process count: {deserialized.Processes?.Count ?? 0}");
```

## CodeWF.NetWrapper

`CodeWF.NetWrapper` はTCP/UDP通信を処理し、生のパケットを強く型付けされたコマンドに変換します:

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

## ファイル転送とファイル管理

`TcpSocketClient` と `TcpSocketServer` は、ファイルの参照、ディレクトリ作成、パス削除、アップロード、ダウンロードなどのAPIを提供します。転送プロセスでは同じ `TaskId` が使用され、リクエスト、レスポンス、チャンクデータ、確認応答、拒否、完了メッセージを関連付けることができます。

重要な動作:

- ファイルチャンクサイズは64 KBです。
- アップロードとダウンロードは両方ともオフセットベースのレジュームをサポートしています。
- ファイル完了後、SHA-256チェックサムが検証されます。
- `FileSaveDirectory` が設定されている場合、サーバーはファイル管理操作を管理ルートディレクトリ内に制限します。

## リポジトリ

- GitHub: [https://github.com/dotnet9/CodeWF.NetWeaver](https://github.com/dotnet9/CodeWF.NetWeaver)
- NuGet: [https://www.nuget.org/packages/CodeWF.NetWeaver](https://www.nuget.org/packages/CodeWF.NetWeaver)