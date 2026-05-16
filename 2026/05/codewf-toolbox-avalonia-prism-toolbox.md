![](https://img1.dotnet9.com/2026/05/codewf-toolbox-avalonia-cover.svg)

今天继续整理一个更偏实用的 .NET 桌面项目：**CodeWF Toolbox，码匠工具箱**。

它不是一个只摆几个按钮的 Avalonia Demo，而是一个已经有 100 多个工具入口的桌面工具箱。项目使用 **C# + Avalonia + Prism + Semi.Avalonia + Ursa**，目标很直接：把开发者日常会反复打开的转换、编码、格式化、校验、生成、查询类工具集中到一个本地桌面应用里。

这次我重点不讲空泛的“跨平台 UI 能力”，而是按现有程序实际能做什么来拆。界面截图也重新做了一版，主窗口尺寸调小了一些，首页加入了常用与推荐工具入口；整体用我个人更喜欢的 **沙漠主题** 展示。

先看一段功能巡览 GIF。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-feature-tour.gif)

## 首页：不再只是空白欢迎页

新版首页比之前更适合作为工具箱入口。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-home-desert-zh-cn.png)

首页上方展示三个核心信息：

- 当前可用工具数量：117。
- 当前功能模块数量：12。
- 当前运行平台：Windows x64。

下面增加了“常用与推荐工具”。如果用户已经使用过工具，就优先显示常用工具；不足 8 个时用推荐工具补齐。第一次打开时也不会显得太空，用户能直接看到时间戳、Base64、GUID、二维码、Hash、日志阅读器这类高频入口。

这个调整虽然不大，但对工具箱体验很关键。桌面工具的首页不应该只是宣传页，它应该让用户能马上开始工作。

## 转换工具：开发者每天都会用到的小功能

转换工具是 CodeWF Toolbox 里最基础、也最常用的一类能力。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-converter-desert-zh-cn.png)

当前转换模块里包含这些独立页面和数据驱动工具：

- 时间戳转换：Unix 时间戳与本地时间互转，支持秒和毫秒。
- Base64 编解码：对 UTF-8 文本进行编码、解码、交换和复制。
- GUID 生成器：批量生成 GUID，并控制大小写、格式。
- YAML 转 JSON、JSON 转 YAML。
- 图片转 ICO 图标。
- 挪车二维码生成器。
- Base64 文件转换器。
- Base64 字符串编码/解码。
- 大小写转换。
- Color 选择器。
- 日期时间转换器。
- 整数进制转换器。
- JSON/TOML/XML/YAML 互转。
- List 转换器、Markdown to HTML、罗马数字转换等。

比如 Base64 编解码页面，输入文本后直接得到编码结果。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-base64-desert-zh-cn.png)

这类功能很小，但它们正是开发者一天里最容易反复打开网页搜索的东西。做成本地工具箱以后，优点是明显的：响应快、离线可用、没有广告、不需要把临时文本贴到外部站点。

## 开发工具：格式化、解析、差异对比和命令辅助

开发模块更偏“写代码过程中的辅助工具”。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-json-format-desert-zh-cn.png)

现有开发类工具覆盖面很宽：

- JSON 格式化、JSON 压缩、JSON 路径提取、JSON 转 CSV。
- YAML 格式化。
- XML 格式化、XML XPath 测试。
- SQL 美化和格式化。
- CSV 转 JSON、CSV 转 Markdown 表格。
- INI 转 JSON、.env 转 JSON。
- HTTP Header 解析。
- Data URL 解析。
- Docker 镜像标签解析。
- Docker Run 转 docker-compose。
- Chmod 计算器。
- Crontab 表达式生成。
- Regex Tester、Regex cheatsheet、正则替换。
- Git 备忘录。
- Hex dump 查看器。
- SemVer 检查与比较。
- Markdown 表格生成器。
- NanoID、UUID v5、随机端口等生成类工具。

上图里的 JSON 格式化页面比较典型：左侧输入原始 JSON，右侧输出格式化结果，还能控制键名排序和缩进空格。

这类工具很适合被抽象成数据驱动表单：输入字段、输出字段、运行函数、自动运行配置都可以由 `ToolSpec` 描述，而不是每个工具都写一套重复 XAML。

## 安全工具：Hash、HMAC、密钥和密码相关能力

安全模块里放的是开发中经常需要临时计算或生成的安全相关内容。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-hash-desert-zh-cn.png)

当前已有功能包括：

- Bcrypt 哈希与比较。
- BIP39 助记词/种子生成。
- AES、TripleDES 文本加密解密。
- Hash 文本：MD5、SHA1、SHA2、SHA3、RIPEMD160。
- HMAC 生成器。
- 密码强度分析。
- PDF 签名检查。
- RSA 密钥对生成。
- Token 生成器。
- ULID 生成器。
- UUID v4 生成器。

截图里是 Hash 文本工具。输入 `CodeWF Toolbox` 后，工具直接生成摘要值。类似功能虽然可以在命令行里做，但桌面工具更适合临时验证、复制和对比。

## Web 工具：HTTP、JWT、URL、MIME、UA 都能处理

Web 模块主要面向前后端联调、接口排查和 Web 元数据处理。

现有工具包括：

- Basic Auth Header 生成。
- 当前设备信息查看。
- HTML 实体转义/反转义。
- HTML WYSIWYG 内容生成。
- HTTP 状态码查询。
- JWT 解析。
- Keycode 信息查看。
- Open Graph / Twitter Meta 标签生成。
- MIME 类型查询。
- OTP 一次性密码生成与验证。
- Outlook SafeLink 解码。
- Slugify 字符串。
- URL 编码/解码。
- URL 解析。
- User-Agent 解析。
- JSON Diff。

这些工具的共同特点是输入输出结构都比较标准，非常适合放进统一的 ToolFramework 里维护。

## 媒体工具：二维码、WiFi 二维码和 SVG 占位图

媒体模块目前不是剪辑软件那类“大媒体工具”，而是偏开发者日常需要的小型生成工具。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-qrcode-desert-zh-cn.png)

现在已有：

- 二维码生成器。
- WiFi 二维码生成器。
- SVG 占位符生成器。

二维码生成器支持输入 URL 或文本，并生成 QR Code。这个功能放在本地工具箱里很方便，尤其是临时把本机地址、文档地址、仓库地址给手机扫的时候。

## 网络工具：IP、CIDR、MAC 地址

网络模块目前覆盖的是 IP 和 MAC 地址这类基础工具：

- IPv4 地址转换器：十进制、二进制、十六进制、IPv6 mapped 等格式。
- IPv4 范围扩展：起止 IP 转 CIDR 范围。
- IPv4 子网计算器。
- IPv6 ULA 生成器。
- MAC 地址生成器。
- MAC 地址厂商查询。

这些工具对后端、运维、网络排查和本地开发环境配置都很实用。

## 数学、计量、文本与数据工具

CodeWF Toolbox 不是只做编码转换，它还把一些小计算和文本处理工具也放进来了。

数学类：

- ETA 计算器。
- 数学表达式计算器。
- 百分比计算器。

计量类：

- Benchmark builder。
- Chronometer 计时器。
- 温度转换器。

文本类：

- ASCII Art 文本生成器。
- Emoji 选择器。
- Lorem ipsum 生成器。
- Numeronym 生成器。
- 字符串混淆器。
- 文本 Diff。
- 文本统计。

数据类：

- IBAN 校验和解析。
- 电话号码解析与格式化。

这些功能不一定每天都用，但它们都属于“用时临时找工具”的场景，放进工具箱后查找成本会低很多。

## 日志阅读器：不是把大文件一次性读进内存

日志阅读器是我比较喜欢的一个模块方向。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-logviewer-desert-zh-cn.png)

它不是简单地把日志文件 `ReadAllText()` 后塞进文本框，而是按可见窗口渲染，面向大日志文件和实时 tail 场景设计。界面上能看到：

- 打开日志。
- 重新加载。
- 跳到末尾。
- 跟随尾部。
- 当前文件路径。
- 可见内容区域。

这类功能对桌面工具来说很有价值。很多时候你只是想快速看一个几百 MB 的日志，不想打开 IDE，也不想让普通文本编辑器卡住。

## 国际化资源管理：XML 翻译文件的合并与管理

项目里还有一个 `CodeWF.Modules.XmlTranslatorManager` 模块，用来处理 XML 语言资源管理。

它包含：

- 合并 XML 文件。
- 管理 XML 文件。
- 多语言属性和语言类模型。

这说明 CodeWF Toolbox 不只是把零散工具堆在一起，它也开始覆盖自己开发过程会遇到的配套问题：工具多了以后，多语言资源、模块菜单、资源合并都需要被工具化。

## 主题与语言：沙漠主题确实更耐看

这次截图我主要用了沙漠主题。整体观感比纯浅色更有辨识度，又不像深色主题那样压暗内容。

主题切换效果可以看这个 GIF：

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-theme-language.gif)

当前主题列表包括：

- 跟随系统。
- 浅色模式。
- 深色模式。
- 水生。
- 沙漠。
- 黄昏。
- 夜空。

语言资源包括：

- 简体中文。
- 繁体中文。
- English。
- 日语。

这里值得注意的是，主题和语言不是简单地换几个字符串。桌面应用要长期可用，主题、控件库、字体、窗口尺寸、列表宽度、下拉框宽度都要一起看。比如这次主窗口就从偏大的启动尺寸收到了更适合日常桌面的比例，同时首页卡片也做了更紧凑的 4 列布局。

## 它是怎么组织这么多工具的

如果每个小工具都写一个完整页面，后期维护会很痛苦。CodeWF Toolbox 现在大致有两种工具组织方式。

第一类是独立页面工具。

例如：

- 时间戳转换。
- Base64 编解码。
- GUID 生成器。
- 图片转 ICO。
- 日志阅读器。
- XML 翻译资源管理。

这类工具有更具体的交互和状态，所以单独写 View 和 ViewModel 更合适。

第二类是数据驱动工具。

例如：

- JSON 格式化。
- JWT 解析。
- Hash 文本。
- URL 编码。
- MIME 查询。
- MAC 地址查询。
- 二维码生成。
- 数学计算。
- 文本统计。

这些工具的输入输出模式更标准，可以用 `ToolSpec` 描述：

```csharp
public class ToolSpec
{
    public string Id { get; set; }
    public string Category { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
    public List<ToolField> Fields { get; } = [];
    public List<ToolOutput> Outputs { get; } = [];
    public Func<ToolRunContext, CancellationToken, Task> RunAsync { get; set; }
    public bool AutoRun { get; set; }
}
```

这样新增工具时，很多情况下只需要补：

- 工具 ID。
- 分类。
- 输入字段。
- 输出字段。
- 算法函数。
- 多语言资源。
- 图标。

UI 表单可以复用，工具逻辑也不会污染主窗口。

## Prism 模块化让功能持续增加

项目入口里通过 Prism 注册模块：

```csharp
moduleCatalog.AddModule<MainModule>();
moduleCatalog.AddModule<ToolFrameworkModule>();
moduleCatalog.AddModule<ConverterModule>();
moduleCatalog.AddModule<DevelopmentModule>();
moduleCatalog.AddModule<SecurityModule>();
moduleCatalog.AddModule<WebModule>();
moduleCatalog.AddModule<MediaModule>();
moduleCatalog.AddModule<NetworkModule>();
moduleCatalog.AddModule<MathModule>();
moduleCatalog.AddModule<MeasurementModule>();
moduleCatalog.AddModule<TextModule>();
moduleCatalog.AddModule<DataModule>();
moduleCatalog.AddModule<LogViewerModule>();
moduleCatalog.AddModule<XmlTranslatorManagerModule>();
```

每个模块负责注册自己的菜单项和 View。主程序不需要知道每个工具页面内部怎么实现，它只负责：

- 应用启动。
- 登录窗口。
- 主窗口。
- 主题和语言。
- 公共服务。
- Prism Region。
- 模块目录。
- 系统托盘和退出行为。

这个边界很重要。工具箱这种项目很容易越写越散，最后主窗口里全是功能细节。Prism 模块化至少给它提供了一个比较清晰的扩展方向。

## 适合学习的点

如果你正在学 Avalonia，这个项目比单窗口 Demo 更值得看，因为它处理了很多真实桌面应用会遇到的问题：

- 主窗口和登录窗口。
- 系统托盘。
- 主题切换。
- 多语言资源。
- 模块化导航。
- ToolFramework 数据驱动工具。
- 文件选择。
- 剪贴板复制。
- 大日志文件查看。
- 用户配置和使用记录。
- Windows/Linux/macOS 平台差异预留。
- 发布配置。

这类工程点往往不是“能不能画出按钮”的问题，而是“工具多了以后还能不能维护”的问题。

## 目前还可以继续打磨的地方

项目已经有比较完整的方向，但仍有继续打磨空间：

- 部分工具页面的控件样式还可以继续统一，比如输入框边框、按钮层级、间距。
- 部分中文资源仍带有机器翻译痕迹，可以逐步润色。
- 工具数量已经很多，后续可以给首页推荐工具做更明确的分类策略。
- 搜索结果可以进一步突出工具分类和最近使用情况。
- 日志阅读器可以加入示例文件或拖拽打开入口。
- 构建仍有预览 SDK 提示、过时 API 和 Avalonia XAML 可达性警告，后续可以逐步清理。

我这次已经把主窗口默认尺寸调小，并把首页补成 8 个快捷入口，沙漠主题下整体更像一个日常可用的工具箱，而不是一个被拉得过大的桌面壳。

## 最后

CodeWF Toolbox 的价值不在于某一个单独工具有多复杂，而在于它把很多开发者高频小工具组织成了一个可扩展的桌面应用。

它现在已经覆盖：

- 转换工具。
- 开发工具。
- 安全工具。
- Web 工具。
- 媒体工具。
- 网络工具。
- 数学工具。
- 计量工具。
- 文本工具。
- 数据工具。
- 日志阅读器。
- 国际化 XML 资源管理。

用 C# 写桌面工具并不过时。对文件、本地剪贴板、日志、离线转换、临时生成、格式化和系统集成这些场景来说，本地桌面应用依然很有价值。

而 Avalonia + Prism 给这个方向提供了一条比较现代的路线：既能跨平台，又能用模块化方式维护大量功能。

如果你想学习 Avalonia，不建议只停留在“画一个窗口”。更值得练的是这种能力：**如何把不断增加的小工具，组织成一个还能长期维护的产品。**
