---
title: "给桌面应用装上 MCP 服务：官方 C# SDK 实战（Vex 为例）"
slug: dotnet-mcp-csharp-sdk-vex
description: "用微软官方 ModelContextProtocol C# SDK 给桌面应用开发 MCP 服务与客户端：最小服务端、业务桥接、Bearer 鉴权、Stateful 会话、Accept 双媒体、SSE 帧、stdio 桥与 Native AOT，全程以 Vex 的真实代码讲解。"
date: 2026-11-12 09:10:00
lastmod: 2026-11-12 09:10:00
cover: 2026/11/dotnet-mcp-csharp-sdk-cover.png
banner: false
categories:
  - .NET
tags:
  - MCP
  - "C#"
  - AI
  - Avalonia
author: dotnet9
draft: false
---

上个月 Vex 的 MCP 支持还是自己手写的 JSON-RPC：HttpListener 收请求、switch 分发方法、自己拼协议版本、自己管会话。十一月初微软 .NET 11 发布时把 [MCP C# SDK](https://github.com/modelcontextprotocol/csharp-sdk) 的服务器模板收进了 SDK，我趁机把 Vex 和 Vox 两个应用的协议层全部换成了官方 [ModelContextProtocol](https://www.nuget.org/packages/ModelContextProtocol)（2.4.0）。迁移下来最深的感受是：**协议层真的可以退休了，值得自己写的只有业务引擎**。

这篇文章以 Vex（Avalonia 桌面 Markdown 编辑器）为例，把服务端、客户端两侧的完整做法过一遍——包括我踩过的四个坑。所有代码都来自 Vex/Vox 仓库的真实提交，可以直接跑。

![MCP × .NET 封面](dotnet-mcp-csharp-sdk-cover.png)

## 1. 准备工作：两个包的分工

官方 SDK 在 NuGet 上是一套三件：

| 包 | 用途 |
| --- | --- |
| `ModelContextProtocol.Core` | 协议类型与核心抽象（Tool、CallToolResult、RequestContext 等），不依赖 ASP.NET Core |
| `ModelContextProtocol.AspNetCore` | Streamable HTTP 传输的 ASP.NET Core 承载（本文主角） |
| `ModelContextProtocol` | 聚合包，顺带把客户端 API 带进来 |

桌面应用内嵌服务的组合是：**业务层引 Core 产协议类型，宿主层引 AspNetCore 承载传输**。Vex 的包引用只有两行：

```xml
<ItemGroup>
  <FrameworkReference Include="Microsoft.AspNetCore.App" />
  <PackageReference Include="ModelContextProtocol.AspNetCore" />
</ItemGroup>
```

`FrameworkReference` 是因为桌面应用默认不带 ASP.NET Core 框架，而 Kestrel 要跑在进程内。

## 2. 最小服务端：三十行以内

MCP 服务本质是一个挂在 Kestrel 上的端点。Vex 的宿主（`McpServerHost`）核心就这几行：

```csharp
var builder = WebApplication.CreateBuilder();
builder.Services
    .AddMcpServer(o =>
    {
        o.ServerInfo = new Implementation
        {
            Name = "vex",
            Title = "Vex",
            Version = typeof(McpServerHost).Assembly.GetName().Version?.ToString(3) ?? "1.0",
        };
    })
    .WithHttpTransport(o => o.SessionMode = HttpServerSessionMode.Stateful)
    .WithListToolsHandler((_, _) => ValueTask.FromResult(_toolDispatcher.ListTools()))
    .WithCallToolHandler(HandleCallAsync)
    .WithListResourcesHandler(async (_, _) => await _toolDispatcher.ListResourcesAsync())
    .WithReadResourceHandler((ctx, _) =>
        ValueTask.FromResult(_toolDispatcher.ReadResource(ctx.Params?.Uri ?? string.Empty)));

var app = builder.Build();
app.Urls.Add($"http://127.0.0.1:{port}");
app.MapMcp("/mcp");
app.StartAsync().GetAwaiter().GetResult();
```

`MapMcp("/mcp")` 一行挂上端点，initialize 握手、`Mcp-Session-Id` 分配、协议版本协商全部由 SDK 处理。应用退出时 `StopAsync` + `DisposeAsync` 收尾即可。

**第一个坑就在这里**：这一版 `HttpServerTransportOptions.SessionMode` 默认是 `Stateless`——服务端不分配会话、不回 `Mcp-Session-Id` 响应头。Claude Desktop 这类依赖会话的客户端会直接懵掉。桌面应用内嵌服务请显式设为 `Stateful`。

还有一个容易忽略的小改名：`McpServerOptions` 上的说明文字字段叫 **`ServerInstructions`**（老资料里的 `Instructions` 已经不存在），它会在 initialize 响应里告诉 AI 客户端"这个服务怎么用"。

## 3. 接上真实业务：handlers 只做桥接

SDK 的 handler 委托是协议与业务的分界线。Vex 的做法是让业务层（`McpToolDispatcher`）直接产出 SDK 的协议类型，handler 里只做参数搬运：

```csharp
private async ValueTask<CallToolResult> HandleCallAsync(
    McpSdk.RequestContext<CallToolRequestParams> context, CancellationToken cancellationToken)
{
    var arguments = context.Params?.Arguments;
    JsonElement? args = arguments is { Count: > 0 }
        ? JsonSerializer.SerializeToElement(arguments)
        : null;
    return await _toolDispatcher.CallToolAsync(context.Params?.Name ?? string.Empty, args);
}
```

`McpToolDispatcher` 里是 Vex 的 29 个工具（读文档、改正文、切主题、导出……），写入类工具会先弹应用内确认对话框，全部调用落审计日志——这些业务逻辑与协议完全无关，换成任何传输都不用动。

工具清单的产出同样直接映射到 SDK 类型：

```csharp
public ListToolsResult ListTools() => new()
{
    Tools =
    [
        new Tool
        {
            Name = "vex_get_current_document",
            Description = _localizer.Get(VexL.McpToolGetDocument),
            InputSchema = JsonDocument.Parse(inputSchema).RootElement.Clone(),
        },
        // ……其余 28 个
    ],
};
```

调用结果统一走一个 helper：

```csharp
private static CallToolResult TextResult(string text, bool isError = false) => new()
{
    Content = [new TextContentBlock { Text = text }],
    IsError = isError,
};
```

**设计建议**：让业务层产出 SDK 的协议类型（而不是在 handler 里做业务对象到 JSON 的转换），类型错误在编译期暴露，业务层也保持对传输方式的无知。

## 4. 鉴权：路由前的中间件

本机服务也不能裸奔——局域网里别的进程同样摸得到 127.0.0.1。Vex 在路由前拦一层 Bearer：

```csharp
app.Use(async (context, next) =>
{
    if (context.Request.Path.StartsWithSegments("/mcp") && !IsAuthorized(context.Request))
    {
        context.Response.StatusCode = 401;
        context.Response.Headers.WWWAuthenticate = "Bearer";
        await context.Response.CompleteAsync();
        return;
    }
    await next(context);
});
```

令牌比较建议用固定时间比较，避免时序侧信道：

```csharp
private static bool Authorized(string? header, string token)
{
    const string prefix = "Bearer ";
    if (!header.StartsWith(prefix, StringComparison.OrdinalIgnoreCase)) return false;
    var supplied = Encoding.UTF8.GetBytes(header[prefix.Length..].Trim());
    var expected = Encoding.UTF8.GetBytes(token);
    return supplied.Length == expected.Length
        && CryptographicOperations.FixedTimeEquals(supplied, expected);
}
```

令牌生成用密码学随机数即可，应用内提供"重新生成"入口（旧的立即作废）。

## 5. 会话、Accept 与 SSE：三个协议细节

迁移过程中真正费时间的不是 API，而是三个协议细节。

**细节一：Stateful 会话**。initialize 之后服务端会分配 `Mcp-Session-Id`，客户端的**每一个后续请求**都必须原样带回这个头——SDK 校验很严格，缺了直接拒绝。第 7 节的测试器会演示怎么拿。

**细节二：Accept 双媒体类型**。MCP 规范要求 POST 请求的 `Accept` 同时声明 `application/json` 与 `text/event-stream`（客户端提前声明自己两种都能收）。SDK 按规范强制校验，缺任何一个直接 **406 Not Acceptable**。Vex 应用内的"测试连接"按钮迁移后第一次就死在这——旧的自研服务器不校验，SDK 按规范办事。

**细节三：回包是 SSE 帧**。服务端可以选 `text/event-stream` 回包，形状是：

```text
event: message
data: {"result":{...},"id":1,"jsonrpc":"2.0"}
```

自写的客户端要按帧解析（取 `data:` 行），不能假设拿到的是裸 JSON。

## 6. 客户端：标准客户端怎么接入

服务跑起来后，标准的 MCP 客户端（Claude Desktop、Cursor 等）用一段配置就能接入。Vex 的偏好设置里内置了这段配置的生成与复制：

```json
{
  "mcpServers": {
    "vex": {
      "url": "http://127.0.0.1:17891/mcp/",
      "headers": {
        "Authorization": "Bearer 30cd7986ea60f3f1a5c44eca09fc9b9603b777d35d0711a0b11ecbeafb5b09"
      }
    }
  }
}
```

把这段贴进客户端的 MCP 配置文件，重启客户端，工具清单就出现了。

## 7. 客户端：自己写一个测试器

应用内最好有一个"测试连接"，让用户不离开应用就能确认服务可用。Vex 的实现是一次真实的 initialize 往返：

```csharp
using var client = new HttpClient { Timeout = TimeSpan.FromSeconds(5) };
using var request = new HttpRequestMessage(HttpMethod.Post, Endpoint);
request.Headers.TryAddWithoutValidation("Authorization", "Bearer " + Token);
// MCP 规范：Accept 必须同时含两种媒体类型，缺一即 406
request.Headers.TryAddWithoutValidation("Accept", "application/json");
request.Headers.TryAddWithoutValidation("Accept", "text/event-stream");
request.Content = new StringContent(
    "{\"jsonrpc\":\"2.0\",\"id\":1,\"method\":\"initialize\",\"params\":{" +
    "\"protocolVersion\":\"2024-11-05\",\"capabilities\":{}," +
    "\"clientInfo\":{\"name\":\"vex\",\"version\":\"1.0\"}}}",
    Encoding.UTF8, "application/json");
using var response = await client.SendAsync(request);
```

Stateful 模式下，initialize 的响应头里带 `Mcp-Session-Id`，**后续的 `tools/list`、`tools/call` 都要带上它**：

```csharp
var session = response.Headers.TryGetValues("Mcp-Session-Id", out var values)
    ? values.FirstOrDefault() : null;

using var listRequest = new HttpRequestMessage(HttpMethod.Post, Endpoint);
listRequest.Headers.Authorization = new AuthenticationHeaderValue("Bearer", Token);
listRequest.Headers.Accept.ParseAdd("application/json");
listRequest.Headers.Accept.ParseAdd("text/event-stream");
listRequest.Headers.TryAddWithoutValidation("Mcp-Session-Id", session);
listRequest.Content = new StringContent(
    "{\"jsonrpc\":\"2.0\",\"id\":2,\"method\":\"tools/list\",\"params\":{}}",
    Encoding.UTF8, "application/json");
```

Vox 的做法更进一步：把会话缓存进服务层（`EnsureUiSessionAsync`），服务停止/重启时失效，模拟调用全部自动携带——测试器、模拟调用与真实客户端走完全相同的协议路径，406 这类问题在应用内就能暴露。

## 8. 客户端：stdio 桥

有些客户端只支持 stdio 传输。Vex 的方案是一个 60 行的 Python 桥（随应用分发）：客户端把桥当 stdio 服务器启动，桥把请求转发到 Vex 的 HTTP 端点。核心的 `post` 函数值得逐行看：

```python
SESSION = {"id": None, "protocol": None}

def post(payload):
    body = json.dumps(payload, ensure_ascii=False).encode("utf-8")
    headers = {
        "Content-Type": "application/json; charset=utf-8",
        "Accept": "application/json, text/event-stream",   # 细节二
        "Authorization": "Bearer " + TOKEN,
    }
    is_initialize = payload.get("method") == "initialize"
    if SESSION["id"]:
        headers["Mcp-Session-Id"] = SESSION["id"]          # 细节一
    if SESSION["protocol"] and not is_initialize:
        headers["MCP-Protocol-Version"] = SESSION["protocol"]
    req = urllib.request.Request(URL, data=body, headers=headers, method="POST")
    with urllib.request.urlopen(req, timeout=120) as resp:
        data = resp.read()
        session_id = resp.headers.get("Mcp-Session-Id")
        content_type = resp.headers.get("Content-Type", "")
    if session_id:
        SESSION["id"] = session_id
    # ……initialize 后记录协商出的协议版本，回包按 SSE 帧解（细节三）
```

initialize 之后从响应头记录会话 ID 与协商出的协议版本，之后的请求分别带回两个头；回包若声明为 `text/event-stream` 就按 `data:` 行解帧、按请求 id 配对响应。通知类消息（无 id）转发但不回写 stdout。整个桥没有第三方依赖，`python mcp_stdio_bridge.py` 配上两个环境变量就能用。

桥的配置方式与第 6 节不同——客户端把桥当本地进程启动：

```json
{
  "mcpServers": {
    "vex": {
      "command": "python",
      "args": ["<Vex安装目录>/mcp_stdio_bridge.py"],
      "env": { "VEX_MCP_TOKEN": "<你的 Token>" }
    }
  }
}
```

## 9. Native AOT：能过，但有一处要注意

Vex 和 Vox 都是 NativeAOT 发布，ASP.NET Core minimal hosting 加 SDK 顺利通过 AOT 与裁剪。唯一要改的是自己写的桥接代码：**反射版 JSON API 在 AOT 下会炸**，`JsonSerializer.SerializeToNode(JsonElement)` 这类调用要换成 AOT 友好的写法：

```csharp
json[key] = JsonNode.Parse(value.GetRawText());
```

改完之后 Vex 的裁剪告警反而从 149 降到 135——自研协议层里那些反射序列化恰恰是告警大户。发布验证可以用 PE 头检查（确认 CLR 目录为空、没有混入 JIT 载荷），Vex 的发布脚本里就带了这个门禁。

## 10. 测试：把传输层当真实服务测

协议迁移最容易漏测的就是传输层。Vex 新建了 `Vex.Mcp.Tests`，用一个假的工具分发器接**真实的 Kestrel + SDK 传输**，六条端到端用例：握手、工具清单、调用往返、参数透传、资源读写、401。夹具里两个细节值得抄：

```csharp
// Accept 双媒体（细节二）
request.Headers.Accept.ParseAdd("application/json");
request.Headers.Accept.ParseAdd("text/event-stream");

// 回包按 SSE 帧解（细节三）：挑承载 JSON-RPC 响应的 data 行
private static string ExtractJson(string body)
{
    string? first = null;
    foreach (var line in body.Split('\n'))
    {
        var trimmed = line.TrimEnd('\r');
        if (!trimmed.StartsWith("data:", StringComparison.Ordinal)) continue;
        var candidate = trimmed["data:".Length..].Trim();
        if (candidate.Contains("\"result\"")) return candidate;
        first ??= candidate;
    }
    return first ?? body;
}
```

另外两条规范回归值得永久钉在测试里：**不带 Accept 的请求必须 406**（防止有人"顺手"去掉头）、**不带会话的 `tools/call` 必须被拒**（钉死 Stateful 行为）。Vex 和 Vox 的测试套件现在各有这两条，之后任何人动传输配置都会被当场拦下。

## 11. 小结

| 事项 | 结论 |
| --- | --- |
| 协议与传输 | `MapMcp("/mcp")` 一行挂端点，握手/会话/协商全归 SDK |
| 业务边界 | handler 只做参数搬运，业务引擎产出 SDK 协议类型 |
| 会话 | 桌面内嵌服务显式 `Stateful`；客户端握手后回传 `Mcp-Session-Id` |
| 请求头 | `Accept` 双媒体缺一即 406 |
| 回包 | 可能是 SSE 帧，自写客户端按 `data:` 行解 |
| AOT | 能过；反射版 JSON API 换 `GetRawText()` |
| 测试 | 真实传输端到端 + 两条规范回归钉死 |

自研协议层那 400 行 JSON-RPC 分发代码在 Vex 里存在了不到两个月。不是它写得不好——是官方 SDK 把"正确"变得比"凑合"更便宜了。

## 12. 参考

1. [modelcontextprotocol/csharp-sdk（官方 C# SDK 仓库）](https://github.com/modelcontextprotocol/csharp-sdk)——API 形状与会话模式的核对出处
2. [Model Context Protocol 官网](https://modelcontextprotocol.io)——Streamable HTTP 传输、会话与 Accept 头的规范依据
3. [Announcing .NET 11（微软官方发布博文）](https://devblogs.microsoft.com/dotnet/announcing-dotnet-11/)——`dotnet new mcpserver` 模板进 SDK 的出处
4. [Vex 仓库](https://github.com/dotnet9/Vex)——本文所有服务端/桥接代码的真实来源（`McpServerHost`、`mcp_stdio_bridge.py`、`Vex.Mcp.Tests`）

---

> 本文在 Vox（基于 .NET 与 Avalonia 的开源内容仓库工具）中写作完成，Vex 与 Vox 的 MCP 服务均已在生产中使用文中方案。
