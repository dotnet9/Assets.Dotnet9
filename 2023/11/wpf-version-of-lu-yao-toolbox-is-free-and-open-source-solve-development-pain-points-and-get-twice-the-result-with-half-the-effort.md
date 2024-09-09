---
title: WPF版【路遥工具箱】免费开源啦！解决开发痛点，让你事半功倍！
slug: wpf-version-of-lu-yao-toolbox-is-free-and-open-source-solve-development-pain-points-and-get-twice-the-result-with-half-the-effort
description: 路遥工具箱是一款基于C# WPF开发的开源工具，旨在解决开发过程中常见的功能性需求，并将其自动化。目前已经拥有十数项实用功能，让你的开发工作事半功倍！
date: 2023-11-14 14:01:16
lastmod: 2023-11-14 15:25:41
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2023/11/cover_05.png
categories: 
    - .NET
tags: 
    - 工具箱
---

路遥工具箱是一款基于C# WPF开发的开源工具箱软件，旨在解决开发过程中常见的功能性需求，并将其自动化。目前已经拥有十数项实用功能，让你的开发工作事半功倍！

- 项目开源地址：https://github.com/landv/LuYao.Toolkit
- 作者网站说明：https://www.coderbusy.com/luyao-toolkit

工具箱功能列表：

![](https://img1.dotnet9.com/2023/11/0502.gif)

## 一、工具箱功能一览

1. 数据生成

- 生成GUID：快速生成唯一标识符。
- 生成密码：自动生成强密码。
- 生成AES密钥：轻松生成AES加密算法所需的密钥。
- 生成RSA密钥：一键生成RSA非对称加密算法所需的公钥和私钥。
- 生成XCode实体：根据JSON数据生成XCode实体类。
- 模板批量生成：根据模板文件批量生成代码。

2. 网络工具

- IP查询：查询指定IP地址的详细信息。
- Ping检测：测试指定主机的网络连通性。
- Whois信息查询：查询指定域名的Whois信息。
- User Agent解析：解析User Agent字符串，获取设备和浏览器信息。
- URL分析器：解析URL，获取各个部分的详细信息。

3. 远程桌面
- 流量监控：实时监控网络流量，帮助你了解网络使用情况。

4. 格式转换

- Unix时间戳转换：将Unix时间戳转换为日期时间。
- RSA密钥格式转换：转换RSA密钥的格式，方便在不同平台使用。
- JSON格式化：美化和格式化JSON数据。
- XML格式化：美化和格式化XML数据。
- 进制转换：支持二进制、八进制、十进制和十六进制之间的转换。
- XSLT转换：使用XSLT样式表转换XML数据。
- JSON转换：支持JSON和其他格式（如XML、YAML、CSV）之间的转换。
- Liquid转换：使用Liquid模板引擎转换数据。
- RGB颜色转换：将RGB颜色值转换为十六进制或CSS颜色名称。
- JSON转C#实体类：根据JSON数据生成C#实体类。
- JSON转CSV：将JSON数据转换为CSV格式。
- Postman数据转换：将Postman导出的数据转换为其他格式。
- Yaml转Json：将Yaml格式的数据转换为Json格式。

5. 文字工具

- 谷歌翻译：使用谷歌翻译API进行文本翻译。
- 多行拼接：将多行文本拼接为单行文本。
- 日志查看器：查看和分析日志文件。
- 全角半角转换：将全角字符转换为半角字符，或反之。
- CSV查看器：查看和编辑CSV文件。
- 正则测试：测试正则表达式是否匹配指定的文本。
- 有道词典：在线查询单词的释义和翻译。
- 哈希计算器：计算文本的哈希值。
- 编码互转：支持常见编码（如UTF-8、GBK、ISO-8859-1）之间的转换。
- 文本压缩：压缩和解压缩文本。
- URL编码：对URL进行编码和解码。
- HTML编码：对HTML代码进行编码和解码。
- ASCII85编码：对ASCII85编码进行编码和解码。
- BASE64编码：对BASE64编码进行编码和解码。
- BASE62编码：对BASE62编码进行编码和解码。
- BASE16编码：对BASE16编码进行编码和解码。

6. 文件处理

- 编码识别：自动识别文件的编码格式。
- 文件校验：校验文件的完整性和一致性。

7. 图片处理

- 图片转图标：将图片转换为ICO图标。
- Gif分割：将GIF动画分割为多个静态图片。
- 图片转Base64：将图片转换为Base64编码。
- Base64转图片：将Base64编码转换为图片。

## 二、项目源码组织结构

这一节只简单介绍如何查看工具箱源码，[源码](https://github.com/landv/LuYao.Toolkit)仓库截图：

![](https://img1.dotnet9.com/2023/11/0501.png)

路遥工具箱的源码组织结构清晰，易于理解和维护。以下是项目组织结构：

![](https://img1.dotnet9.com/2023/11/0503.png)

**如何查看工具箱代码？**

以其中一个【生成 GUID】工具举例。

1. 打开【生成 GUID】工具

点击左侧边栏第2个小图菜单，点击【生成 GUID】：

![](https://img1.dotnet9.com/2023/11/0504.png)

2. 调试状态，点击工具按钮定位视图

标题栏选择【选择元素】，再点击【重新生成】按钮，在VS的实时可视化树可定位到【重新生成】按钮的xaml代码：

![](https://img1.dotnet9.com/2023/11/0505.gif)

既而可以定位到视图代码文件：**`LuYao.Toolkit/Channels/Gens/GenGuid.xml`**

![](https://img1.dotnet9.com/2023/11/0506.png)

【重新生成】按钮绑定的命令是`GenCommand`，接下来查询`ViewModel`功能逻辑代码。

3. 查询命令执行代码

你可以全局搜索`GenCommand`（但你可能搜索不到。。。），但更方便的还是直接查询视图对应的`ViewModel`，功能代码在`LuYao.Toolkit.ViewModels`工程相应的组织（与`GenGuid.xml`文件所在目录相同）目录下`LuYao.Toolkit.ViewModels/Channels/Gens/GenGuidViewModel.cs`

![](https://img1.dotnet9.com/2023/11/0507.png)

命令`GenCommand`和命令处理方法`Gen()`是怎么关联的？

```csharp
[RelayCommand]
private void Gen()
{
    this._guid = Guid.NewGuid();
    var fmt = this.Formats.Find(i => i.IsSelected) ?? this.Formats[0];
    this.Result = fmt.Formater(this._guid);
}
```

`RelayCommand`由框架`CommunityToolkit.Mvvm`提供，由框架自动提供命令与命令处理方法映射关系，具体使用方法请点击[帮助文档](https://learn.microsoft.com/zh-cn/dotnet/communitytoolkit/mvvm/)。

## 三、总结

有兴趣可克隆源码或直接下载工具使用学习，地址还是在Github仓库中：https://github.com/landv/LuYao.Toolkit

以上就是路遥工具箱的主要功能，每个功能都能帮助你提高开发效率，解决开发过程中的痛点，对功能实现感兴趣可打开源码查看。赶快下载体验吧！

- 项目开源地址：https://github.com/landv/LuYao.Toolkit
- 作者网站说明：https://www.coderbusy.com/luyao-toolkit