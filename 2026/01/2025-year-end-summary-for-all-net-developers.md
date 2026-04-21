---
title: 写给所有 .NET 开发者的 2025 年度总结
slug: 2025-year-end-summary-for-all-net-developers
description: 相信今年大家没少看到 《抱歉，C# 已经跌出第一梯队》类似的文章，.NET 生态到底如何，本文将为你系统梳理 2025 年 .NET 开发者最应该关注的技术趋势和重要事件，涵盖AI发展、.NET演进及两者融合的最新动态和趋势，以求帮助大家找准定位，迎接未来的挑战与机遇。
date: 2026-01-05 19:12:04
lastmod: 2026-01-05 19:31:47
cover: https://img1.dotnet9.com/2026/01/cover_01.png
copyright: Reprinted
author: 圣杰|向AI而行
originalTitle: 写给所有 .NET 开发者的 2025 年度总结
originalLink: https://mp.weixin.qq.com/s/kWLLt9duYSazGoSHXN-8bg
categories:
  - .NET
---

![图片](https://img1.dotnet9.com/2026/01/cover_01.png)

# **写给.NET 开发者的2025年度总结**

> 相信今年大家没少看到 *《抱歉，C# 已经跌出第一梯队》*类似的文章，.NET 生态到底如何，本文将为你系统梳理 2025 年 .NET 开发者最应该关注的技术趋势和重要事件，涵盖AI发展、.NET演进及两者融合的最新动态和趋势，以求帮助大家找准定位，迎接未来的挑战与机遇。

------

> 本文由我（圣杰）个人原创撰写，AI 辅助润色完成。
>
> 本文仅陈述事实和表述个人观点，不贩卖焦虑，请放心阅读，文章较长，建议先点赞收藏。
>
> 本文部分内容参考了微软官方博客、.NET 博客、NuGet 统计数据、TIOBE 编程语言指数等公开资料，可能存在疏漏，欢迎指正。
>

## **🎯 2025年的我？**

在开始之前，花几秒钟回顾一下你的2025：

- [ ] 📚 **紧跟潮流，学以致用** — 学了不少新技术，工作中也用上了
- [ ] 🚀 **拥抱AI，效率翻倍** — Vibe Coding真香，已经离不开AI助手
- [ ] 🎉 **收获满满，更上层楼** — 升职加薪/跳槽成功/项目大卖
- [ ] 😰 **AI焦虑，何去何从** — 担心被AI取代，不知道该学什么
- [ ] 😩 **疲于奔命，原地踏步** — 忙于业务，没时间学习新技术
- [ ] 🌱 **转型探索，蓄势待发** — 正在转型或探索新方向
- [ ] 😎 **佛系躺平，岁月静好** — 稳定就好，不想卷了

**👇****👇👇请投票给2025年的自己👇👇👇**

2025年的我？ 

📚 紧跟潮流，学以致用

🚀 拥抱AI，效率翻倍

🎉 收获满满，再上层楼

😰 AI焦虑，何去何从

😩 疲于奔命，原地踏步

🌱 转型探索，蓄势待发

😎 佛系躺平，岁月静好

无论你是哪一种，这篇文章都希望能给你带来一些启发和方向。

**可以的话，欢迎在评论区分享你的2025年感悟以及对2026年的期待**

------

## **前言**

2025年，注定是技术发展史上浓墨重彩的一年。 回顾这一年，AI Agent席卷全球，.NET与AI深度融合，开发者工具百花齐放。作为.NET开发者，你准备好迎接这场变革了吗？

如果说2023年是大模型元年，2024年是大模型落地元年，那么2025年毫无疑问是**AI Agent元年**。从简单的代码补全到能够自主规划、推理、调用工具的智能代理，AI的能力发生了质的飞跃。

与此同时，.NET生态也迎来了重要的里程碑——.NET 10正式发布，C# 语言在TIOBE榜单上持续攀升，NuGet周下载量突破55亿，Visual Studio 2026带来全新体验。更令人兴奋的是，微软在AI领域持续发力，Microsoft.Extensions.AI、Semantic Kernel、Microsoft Agent Framework等框架日趋成熟，.NET开发者拥有了构建AI应用的完整工具链。

本文将从AI发展、.NET演进、.NET+AI融合三个维度，为你梳理2025年.NET开发者最应该关注的技术趋势和重要事件。

------

# **一、AI 的发展**

## **1.1 史上最强 AI 模型不断刷新**

![图片](https://img1.dotnet9.com/2026/01/0101.png)

2025年，AI模型能力突飞猛进，各大厂商你追我赶，不断刷新性能记录。

### **国外模型**

| 厂商          | 模型            | 亮点/特性                                   |
| :------------ | :-------------- | :------------------------------------------ |
| **OpenAI**    | GPT-5.2         | 旗舰通用模型，推理与工具调用显著提升        |
| **Anthropic** | Claude Opus 4.5 | 旗舰，编程与Agent表现最佳，Token效率高      |
| **Google**    | Gemini 3 Pro    | 旗舰模型，强化Deep Research与Gemini App体验 |
| **XAI**       | Grok 4.1        | 卓越的情感智能                              |

### **国内模型**

| 厂商         | 模型          | 亮点/特性                                                    |
| :----------- | :------------ | :----------------------------------------------------------- |
| **阿里云**   | Qwen3         | 全新旗舰，支持119种语言，推理与Agent能力大幅提升，开源版本引领国产大模型生态 |
| **智谱AI**   | GLM-4.7       | 新一代旗舰，编程/Agent/推理/对话全面升级                     |
| **DeepSeek** | DeepSeek-V3.2 | 旗舰模型，Agent能力强化，性价比领先                          |
| **小米**     | Mimo          | 全新旗舰模型，代码能力超过所有开源模型                       |

### **对开发者的影响**

模型能力的提升直接惠及开发者：

- **更强的代码生成**：复杂重构、架构设计、bug修复更加可靠
- **更长的上下文**：处理大型代码库和长文档不再是问题
- **更好的工具调用**：Agent能力大幅增强，自动化程度更高
- **更低的成本**：同等能力下，API调用成本持续下降

## **1.2 Agent时代来临**

![图片](https://img1.dotnet9.com/2026/01/0102.jpg)

2025年，AI从"助手"进化为"代理"。

过去的AI助手（如早期的Copilot）主要扮演"智能补全"的角色——你写一行代码，它帮你补全下一行。而今天的AI Agent则完全不同，它们具备了四大核心能力：

- **规划（Planning）**：能够将复杂任务拆解为多个子任务，制定执行计划
- **推理（Reasoning）**：能够基于上下文进行逻辑推理，做出判断和决策
- **工具调用（Tool Use）**：能够调用外部API、执行代码、操作文件系统
- **记忆（Memory）**：能够记住历史对话和操作，在长期任务中保持上下文

这意味着，AI不再只是"回答问题"，而是能够"完成任务"。你可以告诉AI"帮我重构这个模块的代码"，它会自动分析代码结构、识别问题、制定重构方案、执行修改、运行测试，最终交付一个完整的结果。

GitHub Copilot的Agent模式就是这一趋势的典型代表。当你在VS Code中使用Agent模式时，Copilot不再只是补全代码，而是能够理解你的意图，主动搜索代码库，修改多个文件，运行命令，直到任务完成。

## **1.3 协议的发展：构建AI互操作标准**

![图片](https://img1.dotnet9.com/2026/01/0103.png)

Agent能力的爆发，离不开标准化协议的推动。2025年，四大协议共同构建了AI互操作的基础设施。

### **1. MCP协议 (Model Context Protocol)**

MCP由Anthropic发起，已成为AI工具调用的事实标准。

在MCP出现之前，每个AI应用都需要为不同的工具编写专门的集成代码。MCP统一了AI模型与外部工具的交互方式，定义了：

- **Resources**：AI可以读取的资源（文件、数据库、API响应等）
- **Tools**：AI可以调用的函数（搜索、计算、操作等）
- **Prompts**：预定义的提示模板

有了MCP，开发者只需编写一次MCP Server，就能让所有支持MCP的AI客户端使用这个工具。目前，Claude、VS Code Copilot、Cursor等主流AI工具都已支持MCP协议。

### **2. A2A (Agent-to-Agent)**

A2A由Google发起，定义了Agent之间的通信标准。

在复杂的业务场景中，单个Agent往往无法完成所有任务。例如，一个"旅行规划Agent"可能需要调用"机票预订Agent"、"酒店预订Agent"、"行程规划Agent"等多个专业Agent协同工作。

A2A协议解决的就是这个问题。它定义了：

- **Agent Card**：描述Agent的能力和接口
- **Task**：Agent之间传递的任务请求和响应
- **Message**：Agent之间的通信消息格式

通过A2A，不同团队开发的Agent可以无缝协作，构建更强大的多Agent系统。

### **3. AG-UI (Agent User Interaction Protocol)**

AG-UI定义了Agent与用户界面的交互协议。

传统的AI应用通常是"一问一答"的模式，但Agent执行复杂任务时，用户需要实时了解进度、查看中间结果、提供反馈。AG-UI协议支持：

- **流式UI更新**：实时显示Agent的思考过程和执行状态
- **进度反馈**：展示任务完成百分比和预计剩余时间
- **交互式确认**：在关键节点请求用户确认后再继续

Microsoft Agent Framework已经内置了AG-UI支持，让开发者能够轻松构建用户友好的Agent应用。

### **4. Agent Skills**

Agent Skills是一种开放的标准格式，由Anthropic发起，用于赋予AI Agent新的能力和专业知识。

与其他协议不同，Agent Skills不是一个通信协议，而是一种**知识打包格式**。它让Agent能够按需加载程序性知识和特定上下文（公司级、团队级、用户级），从而更准确、高效地完成任务。

Agent Skills能够实现：

- **领域专业知识**：将专业知识打包为可复用的指令（如法律审查流程、数据分析管道）
- **新增能力**：赋予Agent新能力（如创建PPT、构建MCP Server、分析数据集）
- **可重复工作流**：将多步骤任务转化为一致且可审计的工作流
- **互操作性**：同一个Skill可在不同的Agent产品中复用

目前，Agent Skills已被众多主流AI工具采纳，包括：GitHub Copilot、VS Code、Claude Code、Cursor、OpenAI Codex、Amp、Goose等。

值得注意的是，.NET 10新增的直接运行.cs文件能力，让编写Agent Skills中的脚本变得更加简单——你可以直接用C[#编写工具脚本](javascript:;)，无需创建完整的项目。

## **1.4 AI IDE卷到冒烟**

![图片](https://img1.dotnet9.com/2026/01/0104.png)

2025年，AI编程工具的竞争进入白热化阶段。**你是否已经开启 Vibe Coding模式？**

### **国外产品**

| 产品               | 厂商      | 亮点/特性                                             |
| :----------------- | :-------- | :---------------------------------------------------- |
| **GitHub Copilot** | GitHub    | 市场领导者，Agent模式支持自主任务，深度集成VS Code/VS |
| **Claude Code**    | Anthropic | 终端助手，擅长处理大型代码库，基于Claude模型          |
| **Codex CLI**      | OpenAI    | 命令行工具，轻量快速，适合终端工作流                  |
| **Cursor**         | Cursor    | AI-first IDE代表，体验流畅，支持多模型切换            |
| **Windsurf**       | Codeium   | AI IDE，主打"Flow"概念，强调人机协作                  |
| **Kiro**           | AWS       | 与AWS深度集成，适合云原生开发                         |

### **国内产品**

国内厂商也不甘落后，纷纷推出自己的AI编程工具：

| 产品          | 厂商     | 亮点/特性          |
| :------------ | :------- | :----------------- |
| **CodeBuddy** | 腾讯云   | 支持多种语言和框架 |
| **TRAE**      | 字节跳动 | 基于豆包大模型     |
| **Qoder**     | 阿里巴巴 | 与阿里云深度集成   |

对于.NET开发者来说，有很多选择，但在众多AI编程工具中，**GitHub Copilot**仍然是首选——它与Visual Studio和VS Code的集成最为成熟，对C#的支持也最好。

------

# **二、.NET 的发展**

## **2.1 .NET 10 重磅发布**

![图片](https://img1.dotnet9.com/2026/01/0105.png)

2025年11月，.NET 10正式发布，这是.NET平台的又一个重要里程碑。

### **C# 语言地位提升**

![图片](https://img1.dotnet9.com/2026/01/0106.png)

根据TIOBE编程语言指数，C#在2025年持续攀升，有望成为年度语言。这得益于：

- .NET跨平台能力的持续增强
- Unity游戏开发的持续火爆
- 企业级应用开发的稳定需求
- AI/ML领域.NET生态的完善

### **.NET 10 核心亮点**

![图片](https://img1.dotnet9.com/2026/01/0107.png)

.NET 10延续了性能优先的传统，带来了多项重要改进：

- **性能持续优化**：JIT编译器改进，内存占用更低
- **容器支持增强**：更小的镜像体积，更快的启动速度
- **原生AOT改进**：支持更多场景，编译速度提升
- **云原生增强**：与Kubernetes、容器化部署的更好集成

### **NuGet生态繁荣**

![图片](https://img1.dotnet9.com/2026/01/0108.png)

NuGet周下载量突破55亿，这个数字充分说明了.NET生态的活跃程度。从Web开发到机器学习，从游戏开发到物联网，NuGet上几乎能找到所有你需要的库。

### **.NET 10支持直接运行.cs文件**

这是.NET 10最令人兴奋的特性之一。现在你可以直接运行单个.cs文件：

```shell
dotnet run app.cs
```

不需要创建项目文件，不需要`Program.cs`的样板代码，就像运行Python脚本一样简单。这极大降低了.NET的入门门槛，也让编写快速原型和工具脚本变得更加方便。

### **dnx 登场：.NET 的 "npx/uvx" 时代**

dnx（.NET eXperience）是.NET 10 SDK引入的全新工具执行脚本，本质上是`dotnet tool exec`命令的精简、用户友好的包装器。它标志着.NET正式进入"一次性执行"（one-shot）时代，与Python的`uvx`和Node.js的`npx`完全对标。

#### **核心特性**

- **无需安装即可运行**：直接从NuGet包运行.NET工具，无需永久性本地或全局安装
- **隔离执行环境**：工具包临时下载到NuGet缓存中执行，不修改系统PATH环境变量，确保干净隔离
- **智能版本管理**：默认使用指定工具包的最新版本，支持`@版本号`指定版本；优先使用本地`.config/dotnet-tools.json`中的配置
- **流畅的开发体验**：简化工作流程，降低新工具的尝试门槛

#### **使用示例**

```shell
# 执行C#代码片段
dnx dotnet-execute 'WriteLine("Hello dnx!!!");' --using "static System.Console"

# 快速生成GUID
dnx dotnet-execute "Guid.NewGuid()" 

# 性能压测
dnx LoadTestToolbox hammer --url https://www.example.com --min 1 --max 100

# JSON转YAML
dnx json2yaml -i:input.json -c

# 解码JWT Token
dnx dotnet-decode-jwt <token>
```

#### **与其他平台的对比**

| 特性       | dnx           | uvx          | npx      |
| :--------- | :------------ | :----------- | :------- |
| 按需执行   | ✓             | ✓            | ✓        |
| 版本控制   | @版本号       | @版本号      | @版本号  |
| 隔离执行   | 独立NuGet缓存 | 临时虚拟环境 | 临时下载 |
| 生态成熟度 | 发展中        | 统一uv工具链 | 深度成熟 |

这一创新将NuGet生态推向新的高度，为开发者工具分发开启了全新的可能性。

### **NuGet 包支持分发 MCP Server**

![图片](https://img1.dotnet9.com/2026/01/0109.png)

这是.NET与AI生态融合的重要一步。现在你可以将MCP Server打包为NuGet包进行分发，用户只需安装NuGet包就能获得AI工具能力。这让.NET社区能够更便捷地共享AI工具。

## **2.2 C# 14 新特性**

随着.NET 10一同发布的C# 14，带来了多项实用的语言改进。

### **field 关键字**

现在可以在自动属性中直接访问后备字段：

```csharp
public class Person
{
    public string Name
    {
        get => field;
        set => field = value?.Trim() ?? throw new ArgumentNullException();
    }
}
```

不再需要显式声明私有字段，代码更加简洁。

### **扩展成员 (Extension Members)**

C# 14大幅增强了扩展方法的能力，现在可以定义扩展属性、扩展静态成员等：

```csharp
public extension IntExtensions for int
{
    public bool IsEven => this % 2 == 0;
    public static int Zero => 0;
}

// 使用
int x = 10;
bool even = x.IsEven;  // true
int zero = int.Zero;   // 0
```

### **params集合增强**

`params`关键字现在支持更多集合类型，不仅限于数组：

```csharp
void PrintAll(params IEnumerable<string> items)
{
    foreach (var item in items)
        Console.WriteLine(item);
}

// 可以传入List、Array等任何IEnumerable<string>
PrintAll(["a", "b", "c"]);
PrintAll(new List<string> { "x", "y" });
```

## **2.3 框架生态更新**

### **Aspire 13 发布**

.NET Aspire是微软推出的云原生应用开发框架，2025年迎来了重大升级——Aspire 13。这是迄今为止最大的一次发布：

- **`aspire do` 命令**：全新的构建、发布、部署流水线体验，支持自定义流水线步骤
- **Aspire MCP Server**：Dashboard内置MCP服务器，AI助手可直接查询运行中的应用日志和追踪数据
- **多语言连接字符串**：数据库资源自动暴露多种格式（.NET格式、Python URI格式、Java JDBC格式）
- **JavaScript/Python一等公民支持**：`AddJavaScriptApp`、`AddPythonApp`等全新API
- **全新官网 aspire.dev**：文档和资源全面迁移

### **ASP.NET Core 10**

- **Blazor增强**：`[PersistentState]`声明式状态持久化、电路状态保持、嵌套表单验证、Passkey无密码认证、NotFound页面参数、JS互操作增强
- **Minimal API改进**：内置`AddValidation()`验证支持、`TypedResults.ServerSentEvents`原生SSE、Record类型验证
- **OpenAPI 3.1原生支持**：默认生成3.1规范、YAML格式输出、XML文档注释自动集成
- **认证授权指标**：新增OpenTelemetry认证/授权指标，API端点智能返回401/403

### **MAUI 10**

- **.NET Aspire集成**：新增项目模板，支持OpenTelemetry遥测和服务发现
- **XAML源生成器**：编译时生成强类型代码，全局XML命名空间简化声明
- **控件增强**：HybridWebView支持请求拦截、CollectionView性能优化、MediaPicker多选
- **平台改进**：Android CoreCLR实验性支持、iOS绑定项目可在Windows构建

### **EF Core 10**

EF Core 10作为LTS版本与.NET 10一同发布，带来了多项重要更新：

- **向量搜索支持**：完整支持SQL Server 2025和Azure SQL的`vector`数据类型，通过`SqlVector<float>`和`VectorDistance()`函数实现AI语义搜索和RAG场景
- **JSON数据类型**：原生支持SQL Server 2025的`json`列类型，查询效率大幅提升
- **LeftJoin/RightJoin操作符**：支持.NET 10新增的LINQ左右连接方法，简化复杂查询
- **命名查询过滤器**：支持为实体配置多个命名过滤器，可在查询中选择性禁用
- **复杂类型增强**：支持可选复杂类型、JSON映射、结构体映射
- **ExecuteUpdate支持JSON列**：可高效批量更新JSON文档属性
- **参数化集合改进**：新的默认翻译模式，每个集合值转为独立参数，优化查询计划缓存
- **Cosmos DB全文搜索**：支持全文搜索和混合搜索（RRF函数）

### **ABP 10 发布**

![图片](https://img1.dotnet9.com/2026/01/0110.png)

作为.NET优秀的企业级应用框架，ABP在2025年发布了10.0版本，这是一次重大更新：

- **升级至.NET 10**：全面支持最新的.NET 10 LTS版本
- **AI集成（Volo.Abp.AI）**：提供统一的AI能力集成，支持Microsoft.Extensions.AI、Microsoft Agent Framework和Semantic Kernel，引入AI Workspace概念实现隔离配置
- **新增Workflow模块**：集成Elsa Workflows，支持构建可视化、长期运行的事件驱动工作流
- **Mapperly替代AutoMapper**：采用编译时源生成器，性能更优，无需运行时反射

## **2.4 Visual Studio 2026 发布**

![图片](https://img1.dotnet9.com/2026/01/0111.png)

2025年最令.NET开发者兴奋的消息之一，是Visual Studio 2026的发布。

微软告别了延续多年的年份命名方式，全新的Visual Studio 2026带来了：

- **AI Copilot深度集成**：Agent模式原生支持，无需额外插件
- **性能大幅提升**：启动速度更快，内存占用更低
- **全新UI设计**：现代化的界面，更好的暗色主题支持
- **Git集成增强**：更强大的代码审查和合并体验
- **完整支持.NET 10和C# 14**：开箱即用的最新技术栈支持

------

# **三、.NET + AI 的深度融合**

![图片](https://img1.dotnet9.com/2026/01/0112.png)

2025年，.NET与AI的融合达到了新的高度。微软提供了从底层抽象到上层框架的完整AI开发工具链。

## **3.1 Microsoft.Extensions.AI (MEAI)**

![图片](https://img1.dotnet9.com/2026/01/0113.png)

MEAI是微软推出的AI服务统一抽象层，2025年更新到了10.0版本。

### **核心价值**

MEAI解决的核心问题是：**如何让你的代码不依赖于特定的AI提供商**。

就像`ILogger`让你的日志代码不依赖于特定的日志框架一样，MEAI的`IChatClient`让你的AI代码不依赖于OpenAI、Azure或其他任何提供商。

### **核心接口**

```csharp
// 聊天客户端接口
publicinterfaceIChatClient
{
    Task<ChatCompletion> CompleteAsync(
        IList<ChatMessage> chatMessages,
        ChatOptions? options = null,
        CancellationToken cancellationToken = default);
}

// 嵌入生成接口
publicinterfaceIEmbeddingGenerator<TInput, TEmbedding>
{
    Task<GeneratedEmbeddings<TEmbedding>> GenerateAsync(
        IEnumerable<TInput> values,
        EmbeddingGenerationOptions? options = null,
        CancellationToken cancellationToken = default);
}
```

### **支持的提供商**

MEAI支持多种AI提供商：

- OpenAI / Azure OpenAI
- Anthropic Claude
- Google Gemini
- Ollama（本地模型）
- 更多社区实现...

### **中间件模式**

MEAI支持中间件模式，可以在AI调用链中插入各种处理逻辑：

```csharp
IChatClient client = new ChatClientBuilder(openAIClient)
    .UseLogging()           // 日志记录
    .UseFunctionInvocation() // 函数调用
    .UseRetry()             // 重试策略
    .Build();
```

## **3.2 Semantic Kernel (SK)**

Semantic Kernel是微软2023年就推出的AI编排框架，2025年更新到1.68版本，功能更加成熟。

### **Agent框架**

SK的Agent框架是其核心亮点，支持创建能够自主规划和执行任务的智能代理：

```csharp
var agent = new ChatCompletionAgent
{
    Name = "CodeReviewer",
    Instructions = "你是一个代码审查专家，帮助开发者改进代码质量。",
    Kernel = kernel
};

var response = await agent.InvokeAsync("请审查这段代码...");
```

### **与MEAI深度集成**

SK现在完全基于MEAI构建，这意味着：

- 可以使用任何MEAI支持的AI提供商
- 享受MEAI的中间件能力
- 保持代码的可移植性

## **3.3 Microsoft Agent Framework (MAF)**

![图片](https://img1.dotnet9.com/2026/01/0114.png)

2025年10月，微软正式发布了Microsoft Agent Framework预览版，这是.NET开发者构建AI Agent的全新统一框架。

### **技术基础**

MAF并非从零开始，而是建立在微软已有的AI技术栈之上：

- **Semantic Kernel**：提供强大的编排能力
- **AutoGen**：支持先进的多Agent协作和研究驱动技术
- **Microsoft.Extensions.AI**：提供标准化的AI构建块

MAF是这些技术的演进和统一，为.NET开发者提供了一致的Agent开发体验。

### **核心概念**

MAF将Agent定义为：**结合推理、上下文和工具来追求目标的系统**。

- **推理与决策**：通常由LLM驱动，也可以使用搜索算法、规划系统等
- **上下文感知**：对话历史、知识库、企业数据等外部信息
- **工具使用**：API、MCP工具、代码执行、数据查询等可调用能力

### **工作流类型**

![图片](https://img1.dotnet9.com/2026/01/0115.png)

MAF支持多种工作流模式，满足不同场景需求：

- **Sequential（顺序）**：Agent按顺序执行，结果沿链传递
- **Concurrent（并发）**：多个Agent并行工作
- **Handoff（交接）**：根据上下文在Agent之间转移控制权
- **GroupChat（群聊）**：Agent在共享的实时对话空间中协作

### **代码示例**

创建Agent只需几行代码：

```csharp
// 创建写作Agent
AIAgent writer = new ChatClientAgent(
    chatClient,
    new ChatClientAgentOptions
    {
        Name = "Writer",
        Instructions = "Write stories that are engaging and creative."
    });

// 创建编辑Agent
AIAgent editor = new ChatClientAgent(
    chatClient,
    new ChatClientAgentOptions
    {
        Name = "Editor",
        Instructions = "Make the story more engaging, fix grammar, and enhance the plot."
    });

// 组合为工作流
Workflow workflow = AgentWorkflowBuilder.BuildSequential(writer, editor);
AIAgent workflowAgent = await workflow.AsAgentAsync();

// 执行
var response = await workflowAgent.RunAsync("Write a short story about a haunted house.");
```

### **面向开发者的 DevUI**

![图片](https://img1.dotnet9.com/2026/01/0116.png)

MAF提供了直观的DevUI，帮助开发者可视化设计、调试和监控Agent和工作流，可通过安装`Microsoft.Agents.AI.DevUI`包快速集成。

### **生产就绪特性**

- **ASP.NET集成**：使用熟悉的Minimal API模式暴露Agent服务
- **依赖注入**：通过`AddAIAgent`注册，支持Keyed Services
- **OpenTelemetry**：内置可观测性，一行代码启用遥测
- **评估测试**：集成Microsoft.Extensions.AI.Evaluations进行质量评估

## **3.4 协议SDK全面支持**

![图片](https://img1.dotnet9.com/2026/01/0117.png)

.NET生态现在全面支持AI领域的主要协议，包括MCP、A2A和AG-UI协议。

### **MCP C# SDK**

官方的MCP .NET实现，基于 `ModelContextProtocol` 库，让你轻松创建MCP Server。.NET 10提供了项目模板，一行命令即可创建：

```shell
dotnet new mcpserver -n MyMcpServer
```

使用 `[McpServerTool]` 属性定义工具：

```csharp
[McpServerTool]
[Description("Gets a random number between min and max.")]
public int GetRandomNumber(
    [Description("Minimum value")] int min,
    [Description("Maximum value")] int max)
{
    return Random.Shared.Next(min, max + 1);
}

[McpServerTool]
[Description("Describes random weather in the provided city.")]
public string GetCityWeather(
    [Description("Name of the city")] string city)
{
    var weather = new[] { "sunny", "rainy", "cloudy" };
    return $"The weather in {city} is {weather[Random.Shared.Next(weather.Length)]}.";
}
```

更棒的是，MCP Server可以直接打包为NuGet包分发，用户通过`dnx`命令一键安装使用。

### **A2A C# SDK**

A2A .NET SDK 提供了完整的Agent间通信实现：

```shell
dotnet add package A2A
dotnet add package A2A.AspNetCore
```

**服务端**：通过 `TaskManager` 管理Agent，使用 `MapA2A` 映射端点：

```csharp
var taskManager = new TaskManager();
var agent = new EchoAgent();
agent.Attach(taskManager);  // 注册回调：OnAgentCardQuery、OnMessageReceived

app.MapA2A(taskManager, "/echo");
```

**客户端**：使用 `A2ACardResolver` 发现Agent，`A2AClient` 发送消息：

```csharp
var cardResolver = new A2ACardResolver(new Uri("https://localhost:5048/echo"));
var agentCard = await cardResolver.GetAgentCardAsync();

var client = new A2AClient(new Uri(agentCard.Url));
var response = await client.SendMessageAsync(new MessageSendParams
{
    Message = new AgentMessage
    {
        Role = MessageRole.User,
        Parts = [new TextPart { Text = "Hello!" }]
    }
});
```

**MAF集成**：Microsoft Agent Framework 也提供了A2A协议支持（`Microsoft.Agents.A2A`），可以将MAF Agent暴露为A2A服务端，实现跨框架的Agent互操作。

### **.NET 对 AG-UI 的支持**

 ![图片](https://img1.dotnet9.com/2026/01/0118.png)

MAF框架内置了AG-UI支持，实现Agent与用户界面的流式交互：

```shell
dotnet add package Microsoft.Agents.AI.Hosting.AGUI.AspNetCore  # 服务端
dotnet add package Microsoft.Agents.AI.AGUI                      # 客户端
```

**服务端**：使用 `AddAGUI()` 注册服务，`MapAGUI()` 映射端点：

```csharp
builder.Services.AddAGUI();

AIAgent agent = chatClient.AsIChatClient().CreateAIAgent(
    name: "Assistant",
    instructions: "你是一个友好的AI助手。");

app.MapAGUI("/", agent);  // 自动处理SSE流式响应
```

**客户端**：使用 `AGUIChatClient` 连接服务端，`RunStreamingAsync` 接收流式响应：

```csharp
AGUIChatClient chatClient = new(httpClient, "http://localhost:8888");
AIAgent agent = chatClient.CreateAIAgent(name: "client");
AgentThread thread = agent.GetNewThread();

await foreach (var update in agent.RunStreamingAsync(messages, thread))
{
    if (update.Contents.OfType<TextContent>().FirstOrDefault() is { } text)
        Console.Write(text.Text);
}
```

## **3.5 .NET 对 Agent Skills 的支持**

Agent Skills是一种轻量级、开放的格式，用于扩展AI Agent的能力。一个Skill本质上就是一个包含`SKILL.md`文件的文件夹：

```shell
skill-name/
├── SKILL.md          # 必需：技能描述和使用说明
├── scripts/          # 可选：可执行脚本
│   └── tool.cs
├── references/       # 可选：详细参考文档
│   └── REFERENCE.md
└── assets/           # 可选：静态资源
    └── template.json
```

**SKILL.md格式**：包含YAML frontmatter和Markdown正文：

```shell
---
name: split-pdf
description: Split PDF files into separate single-page documents. Use when you need to divide a PDF into multiple files.
license: MIT
---

# Split PDF

将PDF文件拆分为多个单页文件。

## 使用方法
dotnet scripts/split-pdf.cs input.pdf output-dir/
```

**.NET 10的独特优势**：File-Based Apps让C[#成为Agent](javascript:;) Skills脚本的理想选择：

```csharp
#!/usr/bin/env dotnet
#:package PdfSharpCore@1.3.65
#:package Spectre.Console@0.49.1

using PdfSharpCore.Pdf;
using PdfSharpCore.Pdf.IO;
using Spectre.Console;

if (args.Length < 2)
{
    AnsiConsole.MarkupLine("[red]用法: dotnet split-pdf.cs <PDF文件> <输出目录>[/]");
    return1;
}

var pdfPath = args[0];
var outputDir = args[1];
Directory.CreateDirectory(outputDir);

usingvar doc = PdfReader.Open(pdfPath, PdfDocumentOpenMode.Import);
for (int i = 0; i < doc.PageCount; i++)
{
    usingvar output = new PdfDocument();
    output.AddPage(doc.Pages[i]);
    output.Save(Path.Combine(outputDir, $"page_{i + 1:D3}.pdf"));
}

AnsiConsole.MarkupLine($"[green]✅ 拆分完成！生成 {doc.PageCount} 个文件[/]");
return0;
```

与Python相比，.NET File-Based Apps的优势：依赖声明内联在文件中（`#:package`）、编译时类型检查、支持Native AOT编译实现毫秒级启动。

------

# **四、展望2026**

## **4.1 技术趋势预测**

### **Agent能力持续增强**

2026年，我们将看到更强大的Agent：

- **更长的上下文**：处理更复杂的任务
- **更好的规划**：多步骤任务的成功率提升
- **更强的工具调用**：支持更复杂的工具组合

### **多模态AI成为标配**

图像、音频、视频的理解和生成将成为AI的基础能力，而不是高级功能。.NET开发者需要准备好处理多模态数据。

### **AI原生应用架构演进**

**"AI原生"**将成为新的架构范式，就像"云原生"改变了我们构建应用的方式一样。我们需要重新思考：

- 如何设计AI友好的API
- 如何构建可被AI调用的服务
- 如何处理AI的不确定性

## **4.2 .NET开发者的机遇**

### **AI Agent 开发需求增长**

企业对定制化AI Agent的需求正在爆发。根据Gartner预测，到2026年，超过80%的企业将部署某种形式的AI Agent。熟悉.NET和AI的开发者，有机会在以下领域大展身手：

**企业级应用场景**：

- **智能客服Agent**：基于企业知识库的7x24小时智能问答系统
- **业务流程自动化**：订单处理、报表生成、数据分析等重复性任务的自动化
- **代码助手**：企业内部的代码审查、文档生成、技术债务检测工具
- **DevOps Agent**：自动化部署、监控告警分析、故障诊断

**技术实现路径**：

- **开发MCP Server**：将企业内部系统（ERP、CRM、数据库）封装为MCP工具，供AI调用
- **构建多Agent协作系统**：使用MAF的Workflow模式，实现复杂业务流程的智能编排
- **创建领域专用Agent**：结合.NET生态优势（如金融、制造、医疗），打造行业定制化解决方案

**职业发展机遇**：

- **AI应用架构师**：设计企业级AI应用架构
- **Agent开发工程师**：熟练掌握MEAI/MAF，成为团队核心
- **AI工具开发者**：开发开源MCP Server和Agent Skills，构建个人品牌

### **.NET在AI领域的独特优势**

.NET在AI领域有其独特的优势：

- **性能**：对于需要高性能的AI应用，.NET是理想选择
- **企业基础**：大量企业系统基于.NET构建，AI增强有天然优势
- **工具链成熟**：Visual Studio + Copilot是目前最好的AI辅助开发体验之一
- **生态完善**：MEAI/SK/MAF提供了完整的AI开发工具链

### **持续学习建议**

作为.NET开发者，建议重点关注：

1. **学习Vibe Coding**：提升AI辅助开发效率
2. **关注AI模型发展**：了解最新的AI能力和趋势
3. **掌握MEAI**：这是.NET AI开发的基础
4. **学习Microsoft Agent Framework**：Agent开发的核心框架
5. **了解MCP/AG-UI/A2A协议**：AI工具开发的事实标准
6. **实践Agent开发**：通过实际项目积累经验

------

# **五、总结**

2025年是.NET与AI融合的里程碑之年。

回顾这一年：

- **AI进入Agent时代**：从代码补全到自主完成任务，AI能力发生质变
- **协议标准化推动生态繁荣**：MCP、A2A、AG-UI等协议构建了互操作基础
- **.NET 10带来重要升级**：直接运行.cs文件、性能优化、AI集成增强
- **Visual Studio 2026全新体验**：AI Copilot深度集成，开发效率大幅提升
- **.NET AI工具链日趋成熟**：MEAI、SK、MAF提供了完整的开发能力

对于.NET开发者来说，这是最好的时代。我们有成熟的语言和平台，有完善的工具链，有活跃的社区。现在，我们又有了强大的AI助力。

拥抱AI，不是选择，而是必然。

未来已来，你准备好了吗？

------

*本文写于2025年12月25日，预祝所有开发者新年快乐，身体健康，工作顺心，心想事成，万事如意，家庭幸福美满！*
