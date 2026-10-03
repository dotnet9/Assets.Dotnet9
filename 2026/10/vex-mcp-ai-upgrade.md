> 公众号文章 · 配图见同目录（上传后替换为 img1.dotnet9.com/2026/10/ 地址）

先说结论：**三个月前，[sunyonghuan](https://github.com/sunyonghuan) 给我递来一个 PR（[#2](https://github.com/dotnet9/Vex/pull/2)）；现在，我的开源 Markdown 编辑器 Vex 已经可以被 AI 直接操作了。**

AI 能读到你在编辑器里写的每一个字，能改你的文档、存你的文档、切换主题排版——每一步都会弹窗问你，每一步都留下审计记录。

这篇文章就讲清楚三件事：这个功能怎么用、它是怎么来的、路上踩了什么坑。

![Vex 主界面：左侧大纲，右侧预览，状态栏右下角就是 MCP 指示器](https://img1.dotnet9.com/2026/10/vex-mcp-ai-upgrade-main.png)

## 一、先花 30 秒认识 Vex

Vex 是我维护的开源 Markdown 编辑器（[github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)），基于 Avalonia：

- 源码/预览双栏，多主题多排版，写完一键复制成公众号/知乎/掘金格式
- 跨平台发布（Windows/Linux/macOS），Windows 走 Native AOT 单文件，秒开

## 二、MCP 是什么，Vex 加了什么

MCP（Model Context Protocol）一句话：**给 AI 装一双手。** 没有它，AI 只能在聊天框里给你贴代码，你自己复制粘贴；有了它，AI 直接读你的文档、改你的文档、点你的按钮。

这个 PR 给 Vex 内置了一个本机 MCP 服务：只监听回环地址、Bearer Token 鉴权、三档访问范围（当前文档/当前文件夹/自定义目录），默认关闭，设置里一键打开。

![MCP 设置：启用开关、Token、端点、访问范围三档、确认策略](https://img1.dotnet9.com/2026/10/vex-mcp-ai-upgrade-settings.png)

工具一共 29 个，挑几个感受下：

- 读：当前文档全文、大纲、选区、渲染后 HTML、应用状态
- 写：整体替换、按偏移编辑、**查找替换**、插入、打开/新建文档、保存、撤销/重做
- 遥控：主题、排版、语言、布局、侧栏页签、常用编辑命令、导出、复制公众号富文本

## 三、实战：这篇文章的大纲就是 AI 写的

光说不练假把式。下面是我用 AI 客户端指挥 Vex 写本文大纲的完整过程，六步，每步对应一个真实接口。

**第 1 步：握手。** AI 客户端连上 Vex，协商协议版本、拿到能力清单：

![MCP 握手：protocolVersion 2025-06-18，serverInfo Vex](https://img1.dotnet9.com/2026/10/vex-mcp-ai-upgrade-handshake.png)

**第 2 步：AI 问"你能干什么"。** Vex 报出 29 个工具和参数说明，AI 自己决定用哪个：

![tools/list：29 个工具及描述（随界面语言本地化）](https://img1.dotnet9.com/2026/10/vex-mcp-ai-upgrade-tools.png)

**第 3 步：AI 读状态。** `vex_get_app_status` 返回版本 1.3.0 和当前打开的文档路径——它知道自己在跟谁合作。

**第 4 步：AI 写大纲。** `vex_replace_current_document` 把大纲整体写入，编辑器、预览、大纲侧栏实时刷新，全程不用你碰键盘。

**第 5 步：AI 自检。** `vex_get_document_outline` 读回大纲，12 个标题节点、行号全对，AI 自己确认结构没写歪。

**第 6 步：保存。** `vex_save_current_document` 落盘。磁盘文件和 AI 写入的内容逐字一致。

## 四、你可能会问：那安全吗？

问得好，这也是我合这个 PR 时最上心的部分：

- **人工把关默认开启**：凡是写操作——改文档、存文档、打开外部路径——Vex 都会弹窗，写明工具名、目标文件、要干什么。你点"执行"才动，点"取消"AI 收到的是明确拒绝：

![确认弹窗：工具、目标文件、操作摘要，还能勾选"本次运行内记住此选择"](https://img1.dotnet9.com/2026/10/vex-mcp-ai-upgrade-confirm.png)

- **每一步都留痕**：所有 MCP 操作写入审计日志（`%LOCALAPPDATA%\Vex\mcp-audit.jsonl`），帮助菜单里有审计窗口，AI 什么时候干了什么、成没成功，随时可查：

![审计日志：时间、工具、类型、成败一目了然](https://img1.dotnet9.com/2026/10/vex-mcp-ai-upgrade-audit-file.png)

- **越权直接拒绝**：访问范围设在"当前文档"，AI 就碰不了别的路径；我们实测传 `C:\Windows\win.ini`，直接被打回。

信任建立起来了，设置里还有个"完全访问"开关（关掉确认），熟练后自己选。顺手的话，帮助菜单里点两下就能打开"MCP 操作审计"窗口，不用命令行也能看 AI 干了什么。

## 五、怎么接入？

两种方式：

1. **自定义 AI 客户端**：直接 POST HTTP JSON-RPC 到 `http://127.0.0.1:17891/mcp/`，带上 Bearer Token；
2. **标准 MCP 客户端**（Claude Desktop、Cursor 等）：用仓库附带的桥接脚本 `scripts/mcp_stdio_bridge.py`，配置就三行：

```json
{
  "mcpServers": {
    "vex": {
      "command": "python",
      "args": ["scripts/mcp_stdio_bridge.py"],
      "env": { "VEX_MCP_TOKEN": "<MCP 设置中生成的 Token>" }
    }
  }
}
```

## 六、三个月时间线（快速过一遍）

| 版本 | 关键词 |
| --- | --- |
| v1.1.2.x | 选区撞色修复、Avalonia 12.1.x、CI/CD：打 tag 自动出 6 平台安装包 |
| v1.2.0 | [PR #2](https://github.com/dotnet9/Vex/pull/2) 合入，MCP 上线（24 工具起步）；修复安装器混入 PDB（99.9MB → 35.8MB） |
| 界面改版 | 先做 HTML 原型再全面落地，就是截图里这版 |
| v1.3.0 | 工具 24 → 29、resources 能力、stdio 桥接、审计持久化、per-user 安装修复 |

## 七、踩坑记录（干货）

- **NativeAOT 下菜单全灰**：`{ReflectionBinding}` 依赖运行时反射，AOT 发布后元数据不足，命令解析全部失败，Avalonia 把没命令的菜单渲染成禁用。换回编译期绑定（`{Binding 方法}` + `x:DataType`）就好。JIT 下一切正常、AOT 下才翻车，这类问题不上真机根本发现不了。
- **LLM 算不准字符偏移**：按 offset 编辑文档，AI 十次能错三次。所以 v1.3.0 加了"唯一串查找替换"工具，把定位的活儿还给程序。
- **待修**：单实例二次启动可能覆盖配置文件，下个版本处理。

## 八、致谢

重点感谢 **[sunyonghuan](https://github.com/sunyonghuan)**——[PR #2](https://github.com/dotnet9/Vex/pull/2) 的作者，MCP 从 0 到 1 的实现者。这个功能的全部起点，就是他三个月前递过来的那个 PR。

他自己的开源项目也值得一逛：**[Netor.Madorin（马得令）](https://github.com/sunyonghuan/Netor.Madorin)**——同样基于 Avalonia 的桌面 AI 助手，主打多智能体协作（对话 / 专家 / 工作 / 会议四种模式，还有一场 AI 开会投票的"会议"模式），三通道插件体系里就包含 MCP。换句话说，它既是 Vex MCP 能力的天然客户端，也和 Vex 一样是 Native AOT 的 .NET 跨平台实践。对"用 AI 带起一个团队"感兴趣的朋友，去给人家点个 star。

也感谢每一位提 issue、做测试的朋友。开源的意义大概就是这样：有人递砖，有人添瓦，一个编辑器慢慢长出了 AI 的手。

## 九、下载与反馈

- Vex 介绍与文档：**[doc.codewf.com/apps/vex](https://doc.codewf.com/apps/vex/)**
- 开源仓库：**[github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)**（顺手点个 star，就是持续更新的最大动力）
- v1.3.0 下载：[Releases](https://github.com/dotnet9/Vex/releases)（Windows/Linux/macOS 安装包 + NuGet 包）
- 欢迎提 PR。下一个"看不懂但很心动"的功能，也许就来自你。
