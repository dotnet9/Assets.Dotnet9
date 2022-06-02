作为 `Maui`的先行者，我有话要说，微软你为了让我成为牛 B 的程序员真的是煞费苦心，你一定是觉得我不够牛逼所以针对我，存心想气死我。

好了废话不多说，`Maui`现在也算是正式发布了，我有点想用它来做一个桌面应用最好是支持 `Windows` 和 `Mac`（当然 `Linux`我也想，但是微软不给我），于是我开始着手搞了起来。

按照官方教程，下载安装 `vs2022` 最新预览版（`Mac` 也一样），勾选 `Maui`相关工作负载，创建 `Maui`模板程序，直接编译运行，一顿操作行云流水，顺利的让人直呼有戏。

OK，程序运行完美，Mac 上也很完美，而且一套代码在 `Window` 和 `Mac` 上无缝运行，这很 `nice`，我开始有点信心满满。

作为一名牛逼程序员我必须要有点自己的想法，比如我想做一个无边框的桌面应用，这应该很 `easy`，只需要更改 `windowstyle` 为 none（我以为是这样），因为一直以来都是这样，于是我也准备这样去做。可是找了半天无论是 `Window` 类或是 `MauiWinUIWindow` 还是 `UIWindow` 都没有。

我一定是眼花了，我需要更加仔细一点，我提醒自己，于是我又花了半个小时仔细找了一遍，甚至还看了点 `Maui`的源码（虽然我没看懂）。是的，我没有看错，是真的没有，好吧是我想得太简单了，作为跨平台程序要考虑那么多平台，这不是件容易的事，我安慰自己。
好的此路不通，那么我去看看`Maui`的官方教程，这一定有毕竟是官方教程吗？应该很全面。于是我又花了半个小时，看了一遍教程，很抱歉还是没有，嗯这是一件极其难的事情，毕竟是跨平台吗，可以理解。那我去找找`WinUI`的官方教程吧，毕竟是用的`WinUI3`。
说干就干，我三下五除二直奔主体，功夫不负有心人，终于在标题栏里面找到了答案，虽然有答案，但是我还是想说微软真有你的，你做的这破玩意儿跟 `mfc` 有啥区别几乎就是原生 `api` 的简单封装吗，不愧是你 --- 我的巨硬（此处喊破喉咙） 。

我呵呵一笑，于是写下了 `Maui` `Windows`桌面程序修改标题栏的葵花宝典，只见上面密密麻麻写着坑爹。

秘籍如下：

1. 你一定要有耐心；
2. 你一定要有耐心；
3. 你一定要有耐心；
4. 在使用该方案前你一定要注册 Windows 下的生命周期函数，[参考文档：应用生命周期 - .NET MAUI | Microsoft Docs](https://docs.microsoft.com/zh-cn/dotnet/maui/fundamentals/app-lifecycle)，你也可以不用这个方法，而是在 `app` 下重写 `OnCreate` 函数；

- 注册`生命周期函数`

![](https://img1.dotnet9.com/2022/06/0101.png)

- 重写 `OnCreate` 方法

![](https://img1.dotnet9.com/2022/06/0102.png)

![](https://img1.dotnet9.com/2022/06/0103.png)

5. 你一定要将 `Maui Windows` 下自带的 `ExtendsContentIntoTitleBar` 设置为 `false`（这很重要，另外 `Maui Window` 类下有一个 `SetTitleBar`，对不起这不是你想的那样，怎么修改他都无效，可能是我太水没找到方法）

6. 使用 Maui 窗口句柄（`Windows` 平台），获取 `AppWindow`，修改 `AppWindow` 下的 `TitleBar` 的相关属性，[参考文档：标题栏自定义 - Windows apps | Microsoft Docs ](https://docs.microsoft.com/zh-cn/windows/apps/develop/title-bar?tabs=wasdk)

![](https://img1.dotnet9.com/2022/06/0104.png)

7. 完成以上步骤，那么恭喜你，你可以拥有一个无标题栏的窗口了（实际标题栏还在，只不过和下面的正式内容合并在一起了）；

8. 好了完成了上述步骤，那么你一定也想做个，可以任意控制窗体初始化大小，以及最大化窗口的功能，这很抱歉，这真的是太难了。废话不多说，直接上代码，[参考文档：AppWindow Class (Microsoft.UI.Windowing) - Windows App SDK | Microsoft Docs ](https://docs.microsoft.com/zh-CN/windows/windows-app-sdk/api/winrt/microsoft.ui.windowing.appwindow?view=windows-app-sdk-1.0)

a) 窗体最大化需要调用 `Win32Api`,给窗体发送最大化事件（我做了简单的封装有需要的小伙伴可以找我要，也许还有别的方案只是我太笨）

![](https://img1.dotnet9.com/2022/06/0105.png)

b) 改变窗体大小需要调用 `AppWindow` 的 `MoveAndResize` 注意这个函数内部并没有考虑过 Dpi 缩放的问题，需要你自己解决

![](https://img1.dotnet9.com/2022/06/0106.png)

c) 启用全屏，需要使用 `AppWindowPresenter`，[参考文档：AppWindowPresenter Class (Microsoft.UI.Windowing) - Windows App SDK | Microsoft Docs](https://docs.microsoft.com/zh-CN/windows/windows-app-sdk/api/winrt/microsoft.ui.windowing.appwindowpresenter?view=windows-app-sdk-1.0)

![](https://img1.dotnet9.com/2022/06/0107.png)

终于一个简单的窗体指定窗体大小、最大化窗体、无边框的窗体终于完成了，这确实很简单，满满的都是泪（现在还有一些问题就是标题栏拖动会影响按钮没法点击）。

最后，我只能发出无能狂怒，巨硬，下次请把我当傻子可以吗？
