# CodeWF.Toolbox

![CodeWF.Toolbox](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox.svg)

`CodeWF.Toolbox` 是 CodeWF 体系下的本地桌面工具箱，基于 C#、Avalonia、Prism、Semi.Avalonia 和 Ursa 构建。项目面向开发者日常效率场景，把转换、编码、格式化、校验、生成、查询、日志查看等能力集中到一个可离线运行的跨平台桌面应用中。

项目仓库：[https://github.com/dotnet9/CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox)

![CodeWF.Toolbox 预览](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.svg)

![CodeWF.Toolbox 截图](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.png)

## 项目定位

- 本地优先：常用转换、格式化、生成和查询工具不依赖临时网页，适合处理剪贴板文本、日志、配置和临时数据。
- 模块化桌面应用：使用 Prism 模块目录、依赖注入和 Region 导航组织工具页，主壳只负责窗口、菜单、主题、语言和公共服务。
- 工具规模：当前覆盖 100 多个工具入口，首页按常用与推荐工具提供快捷入口。
- 工程样例：适合学习 Avalonia 桌面应用中的主窗口、登录窗口、系统托盘、主题切换、多语言资源、模块菜单和发布配置。

## 界面预览

首页不再只是欢迎页，而是工具箱入口。顶部展示工具数量、模块数量和当前运行平台，下方提供常用与推荐工具入口。

![CodeWF.Toolbox 首页](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-home-desert-zh-cn.png)

功能巡览可以看到从首页进入转换、开发、安全、媒体、日志等工具页的整体流程。

![CodeWF.Toolbox 功能巡览](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-feature-tour.gif)

转换模块和数据驱动工具页是当前工具箱的主体。Base64、JSON 格式化、Hash、二维码等页面都采用统一的输入、运行、复制和输出习惯。

![CodeWF.Toolbox 转换工具](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-converter-desert-zh-cn.png)

![CodeWF.Toolbox Base64 工具](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-base64-desert-zh-cn.png)

![CodeWF.Toolbox JSON 格式化](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-json-format-desert-zh-cn.png)

![CodeWF.Toolbox Hash 工具](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-hash-desert-zh-cn.png)

![CodeWF.Toolbox 二维码工具](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-qrcode-desert-zh-cn.png)

日志阅读器面向大文件和实时 tail 场景；主题与语言可以在应用内直接切换，方便验证不同地区和不同视觉风格下的界面表现。

![CodeWF.Toolbox 日志阅读器](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-logviewer-desert-zh-cn.png)

![CodeWF.Toolbox 主题与语言](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-theme-language.gif)

## 主要能力

| 分组 | 说明 |
| --- | --- |
| 转换工具 | 时间戳、Base64、GUID、日期时间、图片转 ICO、JSON/TOML/XML/YAML 互转、Markdown to HTML、罗马数字转换等。 |
| 开发工具 | JSON/YAML/XML/SQL/CSV 格式化与转换，HTTP Header、Data URL、Docker、Chmod、Crontab、Regex、Git、Hex dump、SemVer、Markdown 表格等辅助工具。 |
| 安全工具 | Bcrypt、BIP39、AES、TripleDES、Hash、HMAC、密码强度、PDF 签名检查、RSA 密钥、Token、ULID、UUID 等。 |
| Web 工具 | Basic Auth、设备信息、HTML 实体、HTTP 状态码、JWT、Open Graph、MIME、OTP、SafeLink、Slugify、URL、User-Agent、JSON Diff 等。 |
| 媒体工具 | 二维码、WiFi 二维码和 SVG 占位符生成。 |
| 网络工具 | IPv4 地址转换、IPv4 范围到 CIDR、IPv4 子网、IPv6 ULA、MAC 地址生成和厂商查询。 |
| 数学、计量、文本与数据 | ETA、表达式计算、百分比、计时、温度转换、ASCII Art、Emoji、Lorem ipsum、文本 Diff、文本统计、IBAN、电话号码解析等。 |
| 日志阅读器 | 面向大文件日志和实时 tail 场景，只渲染可见区域，避免一次性把大文件读入内存。 |
| 国际化资源管理 | XML 翻译资源的合并、管理和语言属性处理，适合维护多语言资源的一致性。 |

## 工程组织

核心代码位于 `src` 目录：

```text
src/
  CodeWF.Toolbox/                    桌面应用入口、Shell、主窗口、设置、主题、资源和模块目录
  CodeWF.Core/                       公共抽象、服务接口、Region 名称、菜单和通知等基础能力
  CodeWF.Controls/                   公共控件
  CodeWF.Modules.ToolFramework/      数据驱动工具运行框架、工具目录和通用表单能力
  CodeWF.Modules.Converter/          转换工具模块
  CodeWF.Modules.Development/        开发辅助模块
  CodeWF.Modules.Security/           安全工具模块
  CodeWF.Modules.Web/                Web 辅助模块
  CodeWF.Modules.Media/              媒体生成工具模块
  CodeWF.Modules.Network/            网络工具模块
  CodeWF.Modules.Math/               数学工具模块
  CodeWF.Modules.Measurement/        计量工具模块
  CodeWF.Modules.Text/               文本工具模块
  CodeWF.Modules.Data/               数据工具模块
  CodeWF.Modules.LogViewer/          大文件日志查看模块
  CodeWF.Modules.XmlTranslatorManager/ XML 国际化资源管理模块
  CodeWF.Toolbox.Tests/              单元测试工程
```

## 扩展方式

项目里的工具大致分为两类：

- 独立页面工具：适合日志阅读器、Base64、GUID、图片转图标、XML 翻译资源管理这类交互更具体的页面。
- 数据驱动工具：适合 JSON 格式化、JWT 解析、Hash 文本、URL 编码、MIME 查询、MAC 查询、二维码生成、文本统计等输入输出结构稳定的工具。

数据驱动工具通过 `ToolSpec` 描述工具 ID、分类、输入字段、输出字段、运行函数和自动运行配置，复用统一表单和运行框架，减少重复 XAML。

## 快速开始

环境要求以仓库 README 为准，当前主线使用：

- .NET 11 SDK。
- Avalonia 支持的 Windows、macOS 或 Linux 桌面环境。

```powershell
dotnet restore CodeWF.Toolbox.slnx
dotnet build CodeWF.Toolbox.slnx
dotnet run --project src/CodeWF.Toolbox/CodeWF.Toolbox.csproj
```

## 适合关注

- 需要一个本地优先、离线可用的开发者桌面工具箱。
- 想学习 Avalonia + Prism 模块化桌面应用如何组织主壳、菜单、页面和服务。
- 想参考数据驱动工具框架，降低大量小工具的维护成本。
- 想了解桌面应用里的主题切换、多语言资源、系统托盘、日志大文件查看和发布配置。

## 仓库

- GitHub：[https://github.com/dotnet9/CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox)
