# CodeWF.Toolbox

![CodeWF.Toolbox](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox.svg)

`CodeWF.Toolbox` 是一个基于 Avalonia + Prism 的模块化桌面工具箱，面向开发者日常效率场景。项目把主壳、公共服务与功能模块拆开组织，后续新增工具页时只需要扩展模块，不必把应用演化成一个难维护的大窗体工程。

![CodeWF.Toolbox](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox.svg)

![CodeWF.Toolbox 预览](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.svg)

![CodeWF.Toolbox 截图](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.png)

## 特性

- 基于 Avalonia UI、Prism 模块目录、依赖注入与 Region 导航构建。
- 当前模块覆盖 AI 工具、格式转换、开发辅助和 XML 翻译管理。
- 保留面向 Native AOT 发布的目录结构和相关配置。
- 多语言资源支持 `en-US`、`zh-CN`、`zh-Hant` 和 `ja-JP`。
- 菜单、页面和模块的组织方式适合继续新增工具页。

## 内置工具

| 分组 | 说明 |
| --- | --- |
| AI | 提供标题转 slug、提示词翻译和聊天辅助等工具。 |
| 格式转换 | 提供 JSON/YAML、图片转图标等格式转换工具。 |
| 开发辅助 | 提供 JSON/YAML 格式化等日常开发辅助工具。 |
| XML 翻译管理 | 用于比对、合并和维护 XML 国际化资源。 |

## 目录结构

```text
src/
  CodeWF.Toolbox/                    主桌面应用、壳、设置与资源
  CodeWF.Core/                       公共抽象、服务与区域定义
  CodeWF.Controls/                   公共控件
  CodeWF.Modules.AI/                 AI 工具模块
  CodeWF.Modules.Converter/          格式转换模块
  CodeWF.Modules.Development/        开发辅助模块
  CodeWF.Modules.XmlTranslatorManager/ XML 国际化管理模块
tests/
  AvaloniaAotDemo/                   Avalonia AOT 示例
  WinFormsAotDemo/                   WinForms AOT 示例
  ConsoleAotDemo/                    控制台 AOT 示例
docs/
  assets/                            独立 SVG 图示
```

## 快速开始

环境要求：

- .NET 9 SDK。
- Avalonia 支持的桌面环境。当前已测试 Windows 7、Windows Server 2019、Windows 10、Windows 11 和 macOS 11+。

```powershell
dotnet restore CodeWF.Toolbox.sln
dotnet build CodeWF.Toolbox.sln
dotnet run --project src/CodeWF.Toolbox/CodeWF.Toolbox.csproj
```

## 仓库

- GitHub：[https://github.com/dotnet9/CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox)
