---
title: 在大公司工作之后才真正领悟到它真的是宇宙级编辑器
slug: after-working-in-a-large-company-i-truly-realized-that-it-is-a-universe-level-editor
description: VS Code使用技巧分享
date: 2024-04-17 07:23:47
lastmod: 2024-04-17 07:39:48
copyright: Reprinted
banner: false
author: React777
originalTitle: 在大公司工作之后才真正领悟到它真的是宇宙级编辑器
originalLink: https://mp.weixin.qq.com/s/JbQ01MNZjKsO0aW7qcU6pQ
draft: false
cover: https://img1.dotnet9.com/2024/04/0201.gif
categories: 分享
tags: VS Code
---

## 开胃菜

我们在用 vscode 写代码时候经常需要选中文本，比如下图

![图片](https://img1.dotnet9.com/2024/04/0201.gif)

但绝大多数情况下我们想选中的是整个类名，如下图

![图片](https://img1.dotnet9.com/2024/04/0202.gif)

其实这个就牵扯到了 vscode 的分词机制，它认为`-`应该截断文本

其实不止`-`，还有其他字符都被 vscode 认为是分隔符

所以贴心的它提供了如下配置

```json
{
  // 如下是被vscode认为是分隔符的字符
  // 我们在设置中搜索editor.wordSeparators
  // 然后根据自己的需要删除不想要的分隔符即可
  // 比如删除@，这样我们就可以直接选中less变量和装饰器如@xxx
  "editor.wordSeparators": "`~#!@$%^&*()-=+[{]}\\|;:'\",<>/?."
}
```

我把 webstorm 的设置全看了一遍，没有找到类似配置，如果有大佬知道怎么在 webstorm 配置的还请评论区留言指教

如果你觉得上方这种小配置可以提升你的代码编写效率以及幸福感，那就继续往下看吧，一定不会让你失望>\_<

## 与 vscode 并不美丽的邂逅

本人是通过 Java 入门编程的，接触的第一个编辑器是 ecilpse，后来 idea 越来越火，让我第一次接触到了 jetbrains 家的编辑器，刚开始使用 idea 时我就受到了深深的震撼，原来编辑器之间亦有差距。后来机缘巧合成为了前端，第一次接触到同为 jb 家出品的真正的前端编辑器 webstorm，由于 idea 使用的很熟，所以基本无缝切换。

因为本人一直对数学很感兴趣，小时候就参加过奥数比赛，接触过一些算法题，再加上有 java 开发经验，所以 typescript 上手也很快且很感兴趣。因为擅长这两项，所以即使学历平庸，甚至都不是计算机相关专业，但仍然可以得到 jz 及 la 的面试官认可并与他们成为同学

犹记得刚进 jz 的时候，因为公司有提供正版 jetbrains 开发工具，但我发现身边的同学都在使用免费的 vscode，所以让我非常诧异，为什么放着 webstorm 不用，都去用 vscode。肯定是收费的更好用啊！所以小组内只有我一个人在使用 webstorm，后来因为安全原因我们组必须要安装一个插件，但是一开始只有 vscode 版本的，也就意味着必须要使用 vscode，我有尝试使用，但是发现她就和一张白纸一样，令我很不习惯，所以我拒绝使用，甚至和 mentor 吵了一架。

现在想来，估计是我当初付费购买的时候就已经被它给 CPU 了，为了说服自己不是冤大头，自己也在催眠自己吧。在此不得不感谢那位 mentor，我当时应该是魔怔了，但她仍然不离不弃，甚至找了心理部门的同学给我做心理辅导及插件开发组的同学为我讲解 vscode 的相关配置，让开发更搞笑。在此，对生命中遇到的那些给与过我帮助的人，说一声谢谢——这个世界并不温柔，但有些人真的很温柔^\_^

话不多说，下面进入正题。因为前端开发领域各不相同，所以我会进行分类讲解。

## 通用

### 字符串里的文件路径快速跳转到对应文件中

有些字符串里的文件路径支持 cmd+点击跳转。如大多数的导入语句，见下图

![图片](https://img1.dotnet9.com/2024/04/0203.gif)

但其他位置的字符串里的文件路径大多不支持跳转，如下图

![图片](https://img1.dotnet9.com/2024/04/0204.png)

安装插件

https://marketplace.visualstudio.com/items?itemName=duXing.quick-go-to-selected-file-path

将鼠标光标放到包含文件路径的字符串上，使用 cmd + e（Windows 系统 ctrl + e）

即可弹出最匹配的文件查询，就我使用的情况来看，第一个总是最匹配的。

如果默认行为不符合，我们可以手动选择字符串，它就会按我们选择的字符串精确搜索

如果这个快捷键不符合你的要求，可以自行修改

![图片](https://img1.dotnet9.com/2024/04/0205.gif)

### 更快捷清晰的打印调试

有时候我们为了排查问题，需要打印一些东西，其实大部分情况下这属于一种模板操作

如下图，复制要打印的变量，然后输入自定义代码片段

```json
"log打印": {
  "prefix": "clog",
  "body": ["console.log('[ $CLIPBOARD ] >', $CLIPBOARD)$0"],
  "description": "log打印",
  "scope": "typescript,typescriptreact,javascript,javascriptreact"
},
```

![图片](https://img1.dotnet9.com/2024/04/0206.gif)

其实可以通过设置宏一步完成

![图片](https://img1.dotnet9.com/2024/04/0207.gif)

也可以安装插件实现,类似的插件有很多

我举其中一个，感兴趣的可以自行搜索 console 关键词就行

https://marketplace.visualstudio.com/items?itemName=ChakrounAnas.turbo-console-log

在此顺便推荐一个非常好用的 vscode 代码片段生成工具

https://snippet-generator.app/?description=&tabtrigger=&snippet=&mode=vscode

举个例子，把自己常用的代码片段或 webstorm 里好用的代码片段转移过来

![图片](https://img1.dotnet9.com/2024/04/0208.jpg)

### 清晰的代码高亮

查看代码时不需要进入对应文件，甚至都不需要鼠标 hovre 就能知道一段代码到底是什么类型

看到紫色的加粗属性我就知道它是只读的

看到绿色斜体我就知道它是枚举,紫色斜体是枚举值

看到黄色下划线我就知道它是被 async 修饰的方法，我下意识就会考虑到要不要加 await 调用，虽然 eslint 之类的也能检测到，但它只会一刀切的全部警告。实际上并不一定要加 await 修饰，所有都警告一定是好的吗？无用的警告，甚至错误的警告只会影响写代码的心情。它应该表达一种语义，用我喜欢的不同于变量，属性之类的其他颜色就很好

![图片](https://img1.dotnet9.com/2024/04/0209.gif)

### 更快捷的功能入口

资源管理器以及源代码管理面板里的功能 tab 是可以拖动或者隐藏的

可以隐藏不想要的 tab 让资源管理器更加清爽

也可以把一些常用的 gitlens 的 tab 拖出来，让 git 视图空间更大,更清晰

![图片](https://img1.dotnet9.com/2024/04/0210.gif)

### 让文件夹层级更清晰

默认情况下，如果一个文件夹下面只有一个文件夹，那么这两个文件夹会合并展示

如下图，style 的下层文件夹是 public 吗？

不，其实是 css，这种不统一的展示至少会让我有些困惑

![图片](https://img1.dotnet9.com/2024/04/0211.png)

更改如下配置即可展示为下图样子

```json
"explorer.compactFolders": false
```

![图片](https://img1.dotnet9.com/2024/04/0212.png)

### 快速打开并利用 vscode 自身强大的功能修改非项目文件

有时候我们安装使用一些工具时需要修改配置文件或是 host 文件啥的，那些文章一般都会说使用 vim 之类的命令，说实话真的很难用，不仅看着不清晰，修改更是不方便。如下图

![图片](https://img1.dotnet9.com/2024/04/0213.gif)

其实 vscode 提供了一个 code 命令，可以很方便的打开文件或文件夹，同样是修改上述文件

![图片](https://img1.dotnet9.com/2024/04/0214.gif)

### 自动复制终端中选择的文本

有时候我们在启动项目时会出现一些报错需要通过百度去解决

这时候就需要去搜索一些终端打印的关键字，配置自动复制的话就可以省去手动复制的操作

```json
"terminal.integrated.copyOnSelection": true
```

### 禁止通过拖放来移动选中内容

有时候选中了一些文本，但因为误触不小心把代码移到了别处，如下

![图片](https://img1.dotnet9.com/2024/04/0215.gif)

这个因人而异，我是不需要这个功能，反而有时候因为误触让我很困惑，配置如下

```json
// 改为false即可禁止拖动
"editor.dragAndDrop": false
```

## 代码提示与跳转

### package.json

有些包的版本使用了^修饰，导致版本并未完全锁定，有时候可能某个小版本有问题需要我们排查

这个时候就需要知道我们具体安装了哪个版本

通过下图我们可以知道当前安装的版本，最新版本，也可以直接进入查看细节

![图片](https://img1.dotnet9.com/2024/04/0216.gif)

我们知道有些包是间接依赖的，并没有列在 package.json 里，我想知道 node_moduels 里到底有没有，可以直接搜索，以 lodash 为例，如下图

![图片](https://img1.dotnet9.com/2024/04/0217.gif)

### Vue

#### 属性与方法等提示和跳转

![图片](https://img1.dotnet9.com/2024/04/0218.gif)

#### css 类名提示和跳转

![图片](https://img1.dotnet9.com/2024/04/0219.gif)

#### vuex 跳转

![图片](https://img1.dotnet9.com/2024/04/0220.gif)

#### 文件路径提示与跳转

![图片](https://img1.dotnet9.com/2024/04/0221.gif)

### React

本人主用 react, 个人认为 vscode 对 react 的支持比 vue 更好，上面 vue 能做到的, react 也都能做到，就不赘述了

这里简单再举个 css 的例子

![图片](https://img1.dotnet9.com/2024/04/0222.gif)

顺便就此说一个相关的，属性的值，如 className 到底是字符串还是 jsx 表达式，vscode 默认是自动推断的，也就是说 className 默认是字符串。

由此就出现了一个问题，比如本人项目中使用的都是 cssmodule，其实需要输入 jsx 表达式。其实倒不如这样说，绝大多数属性值，包括 className，其实我想输入的都是 jsx 表达式，可以进行如下配置

```json
"javascript.preferences.jsxAttributeCompletionStyle": "braces",
"typescript.preferences.jsxAttributeCompletionStyle": "braces"
```

![图片](https://img1.dotnet9.com/2024/04/0223.gif)

话说 webstorm，你连大小写都不认识了吗，这样会生效吗，你还\***\*跳转？？？真是**！

这里就不得不说 webstorm 的一大缺点，总是喜欢强行提示，给人一种仿佛很智能的感觉。所以很吃性能，容易卡顿

![图片](https://img1.dotnet9.com/2024/04/0224.gif)

vscode 不仅可以正确提示，对于错误也有很清晰的解释，并且提供了解决方案

![图片](https://img1.dotnet9.com/2024/04/0225.gif)

## 结语

作为一个使用 webstorm 远超两年半的开发者，vscode 仅用半个月就通过它强大的代码提示与搜索及代码跳转让我有更加高效的开发效率，难怪大公司的员工可以免费使用的情况下都不愿意看 webstorm 一眼，基本都在使用 vscode。

本人最近在准备晋升材料，所以有点忙。且由于配置稍微有点多，为了不耽误大家阅读，我只截取了一部分功能。

如果读者老爷有遇到什么痛点都可以在评论区留言，我会一一解决，请相信我的能力！等我晋升之后基本就不用写代码了，到时候会把文章补全并且就 vscode 的技巧开个专题。

俗话说，工欲善其事，必先利其器。有一个趁手的编辑器可以极大提升自己的开发效率及幸福感。毕竟跟编辑器相处的时间甚至比跟对象相处的时间还要长 ╮(╯▽╰)╭。

webstorm 在某些人眼里或许很好用，但我愿称 vscode 为宇宙级编辑器(#^.^#)。

也许，在程序员的世界，收费的并不一定就是最好的吧！！！
