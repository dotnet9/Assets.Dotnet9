> 转载声明：本文经原作者 **wackyxk** 授权，由 CodeWF 转载发布。
>
> 原文首发于微信公众号「wacky的碎碎念」，版权归原作者所有。
>
> 原文链接：[阅读原文](https://mp.weixin.qq.com/s/39pXYt8j3bhTZQzThNLTVA)
>
> 本文仅作 Markdown 排版与图片本地化处理，未对原文观点作实质性修改。

---

## 前言

大家好，我是wacky。前两篇我们聊了通信协议和数据与业务层——怎么把数据从PLC搬过来、怎么存、怎么管。数据有了，业务逻辑有了，最后一公里是：怎么把这些东西呈现给用户。

这一篇是工控上位机技术栈的第三层——展示与交互层。说白了就是界面。工控软件的界面名声一直不太好，"土味工控"几乎成了行业标签——灰色的按钮、密密麻麻的数字、上世纪的配色、点一下闪一下的刷新。但实际情况是.NET生态在UI方面已经相当成熟，能做出既好看又好用的工控界面，工具链完全具备。

今天聊三件事：界面框架怎么选、可视化控件怎么用、怎么让工业软件告别"土味"。

## 界面框架：WinForm、WPF还是Avalonia

.NET工控界面开发，三个框架各有千秋。

WinForm是工控界的老牌选手。诞生于2002年，至今仍在大量项目中使用。它的优势是简单直接：拖控件-\>写事件-\>跑起来，学习曲线极低。对于那种"几个按钮加几个文本框"的简单上位机界面，WinForm是最快的选择。工控现场很多老系统都是WinForm写的，维护成本极低。

但WinForm的短板也很明显：不支持数据绑定（要用事件手动刷新界面），不支持硬件加速（界面全靠CPU渲染），控件样式定制困难（想做个圆角按钮都得自己画），DPI缩放表现一般（在高分屏上容易糊）。如果你的界面需要复杂的数据展示、动画效果，或者要在4K屏上清晰显示，WinForm就开始力不从心了。

WPF是WinForm的继任者。它引入了XAML机制：一种用XML描述界面的标记语言，配合数据绑定，界面和逻辑彻底分离。WPF的核心优势有三个。

第一，数据绑定。你不需要手动写"读取数据→更新文本框"的代码，只要把界面的控件和数据源绑定好，数据一变，界面自动刷新。这在工控场景下太重要了，PLC每秒推几百个数据点，手动刷新界面会卡死，数据绑定让这一切自动且高效。

第二，样式和模板。WPF的控件外观是完全可定制的，想做什么风格做什么风格，圆角、渐变、阴影、动画都支持。告别"灰色按钮"全靠这一招。

第三，硬件加速。WPF基于DirectX渲染，界面绘制走GPU，不占CPU。工控上位机本来就忙——通信、数据处理、报警判断都在跑，界面渲染再吃CPU就太浪费了。

一个典型的WPF数据绑定示例——把PLC温度值绑定到界面文本框：

```csharp
// ViewModel：实现INotifyPropertyChanged，属性变化时通知界面更新
    public class MainViewModel : INotifyPropertyChanged
    {

        private float _temperature;

        public float Temperature
        {
            get => _temperature;
            set
            {
                _temperature = value;
                OnPropertyChanged(nameof(Temperature));
            }
        }

        public event PropertyChangedEventHandler PropertyChanged;
        protected void OnPropertyChanged(string name) => PropertyChanged?.Invoke(this, new PropertyChangedEventArgs(name));
    }
```

每当PLC推送一个新温度值，你只需要更新ViewModel的Temperature属性，界面上的文本框就会自动刷新，不用写一行操作控件的代码。这就是WPF数据绑定的威力。而Avalonia是近年来的新选择，定位是"跨平台的WPF"。API设计和WPF非常接近，XAML写法几乎一样，但它能跑在Windows、Linux、macOS上。如果你的上位机需要部署到Linux工业平板或者使用国产操作系统，Avalonia是目前最现实的方案。不过Avalonia的生态成熟度还不如WPF——第三方控件库少一些，踩坑的概率高一些。新项目可以尝试，老项目迁移要慎重。我的建议：新项目首选WPF，生态成熟、资料多、控件丰富。简单的工具类界面用WinForm，快速搞定。跨平台需求才考虑Avalonia。实时图表：ScottPlot还是OxyPlot工控界面少不了实时曲线——温度趋势、压力波动、产量统计。选图表库的关键是：能不能扛住高频刷新。工控场景的图表有个特殊性：数据是持续推送的，每秒可能新增几十到几百个数据点，界面需要不断重绘。很多通用图表库在这种场景下会卡顿——每秒重绘几十次，CPU直接拉满。ScottPlot是.NET生态中性能最强的实时图表库。它用ImageSharp做底层渲染，不走WPF的渲染管线，直接画到位图上再贴到界面。这种方式的特点是：渲染速度极快，10万个数据点实时滚动也不卡。缺点是它不是原生WPF控件，交互（缩放、选取）体验不如OxyPlot流畅。OxyPlot是另一个主流选择，WPF原生控件，交互体验好——鼠标缩放、平移、十字光标都很流畅。支持的图表类型也比ScottPlot丰富。但性能上不如ScottPlot，数据点超过几千个、刷新频率高于每秒10次时，可能出现卡顿。具体选哪个？看你的数据量和刷新频率。高频大量数据（比如每秒100个点以上、持续滚动）选ScottPlot。交互优先、数据量中等选OxyPlot。两个都是开源免费，NuGet一键安装。ScottPlot画一条实时滚动的温度曲线，核心代码就这么几行：

```csharp
// 初始化图表，WPF中用WpfPlot控件
            var plot = WpfPlot1.Plot;
            plot.Title("EVO500 主轴温度实时监控");
            plot.XLabel("时间");
            plot.YLabel("温度 (℃)");
            plot.Axes.SetLimitsY(0, 120);// Y轴固定范围
            // 添加一条曲线
            var temperatureBuffer = new double[1000];
            var signal = plot.Add.Signal(temperatureBuffer);
            signal.Color = Colors.Gray;
            signal.LineWidth = 2;
            // 定时器回调：新数据来了之后刷新图表
            DispatcherTimer timer = new DispatcherTimer { Interval = TimeSpan.FromMilliseconds(100) };
            timer.Tick += (s, e) =>
            {
                // temperatureBuffer是循环数组，新数据覆盖旧数据
                WpfPlot1.Refresh();
            };
            timer.Start();
```

对应的前端代码：

```csharp
<Window x:Class="WpfApp3.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:WpfApp3"
        xmlns:sp="clr-namespace:ScottPlot.WPF;assembly=ScottPlot.WPF"
        mc:Ignorable="d"
        Title="MainWindow" Height="450" Width="800">
    <Grid>
        <sp:WpfPlot x:Name="WpfPlot1"/>
    </Grid>
</Window>
```

这段代码的效果是：每100毫秒刷新一次图表，温度曲线从右向左滚动，像心电监护仪那样。对工控操作员来说，这种实时滚动的曲线比一堆静态数字直观得多。

## 控件库：HandyControl告别土味

选好了框架和图表库，还差一样东西——控件库。WPF自带的控件功能够用，但默认样式确实……工业风十足，不是好的那种工业风。

HandyControl是一套开源的WPF控件库，提供超过80种常用控件和丰富的样式主题。装上之后，你的按钮、输入框、下拉框、开关、进度条……全部焕然一新。几行配置就能把整个应用的视觉风格拉上一个档次。

除了HandyControl，还有几个值得关注的：

MaterialDesignInXamlToolkit，谷歌Material Design风格的WPF实现，控件丰富、文档完善，适合喜欢扁平化现代风格的场景。MaterialDesignExtensions在其基础上进一步扩展了导航、对话框等组件。

HandyControl的优势是风格更灵活，中式开发文档更全，工控场景的适配性更好——比如它的Growl通知控件（类似消息提示）很适合做报警弹窗，Stepbar步骤条适合展示设备流程状态。

需要注意的是，控件库别贪多。挑一个主力库用到底，别HandyControl和MaterialDesign混着装，这样会导致两套样式系统打架，调试样式问题会让你怀疑人生。

## 让工业软件不再"丑"的三个原则

工具准备好了，但"好看"不只是控件库的问题。我见过太多项目装了HandyControl，界面照样丑……因为审美和设计原则没有跟上。三个原则，记住就能避开80%的"土味"。

第一，留白。工控界面最大的通病是信息密度过高——恨不得一屏塞进所有数据。结果每个数字都很小、每个控件都挤在一起，操作员看不过来。正确的做法是：主信息大、次信息小、无关信息不显示。一屏只展示当前最关键的数据，历史数据和详细参数放二级页面。留白不是浪费空间，是让重要信息凸显出来。

第二，配色克制。工控界面不需要五颜六色。选一个主色调（比如蓝色），一个强调色（比如橙色用于报警），一个背景色（深灰或浅灰），三个颜色足够。状态颜色遵循行业惯例——绿色正常、黄色警告、红色故障——别标新立异。最忌讳的是用纯色——纯红、纯绿、纯蓝饱和度太高，看久了眼睛累。把饱和度降两档，立刻舒服很多。

第三，反馈即时。工控操作员点击一个按钮，界面必须立即给出反馈——按钮变色、弹提示、状态更新，不能点了没反应等两秒才跳。即使后台操作需要时间，也要先给个"处理中"的反馈。WPF里用动画和异步任务配合就能实现，不复杂。

## 一个完整的展示层架构

把前面聊的串起来，一个典型的.NET工控上位机展示层架构是这样的：

![工控展示层架构](https://img1.dotnet9.com/2026/08/dotnet-industrial-control-display-architecture.png)

界面层用WPF + MVVM模式。Views负责界面布局，ViewModels负责数据绑定和交互逻辑，Models对接服务层的数据接口。HandyControl提供控件样式，ScottPlot画实时曲线，OxyPlot画历史趋势图。

MVVM是WPF的标准架构模式，和上一篇聊的分层一脉相承——ViewModel就是展示层和业务逻辑层之间的桥梁。ViewModel调用业务逻辑层的服务接口拿数据，通过数据绑定推送到View，用户操作View时通过命令绑定触发ViewModel的逻辑。

这套架构的好处是：界面和逻辑彻底解耦。设计师可以用Blend调界面，不影响你写代码；你想换控件库，不影响业务逻辑；甚至想把WPF换成Avalonia，ViewModel层基本不用改。

## 后记

展示层是工控上位机的"门面"——用户看不到你的通信协议多优雅、数据层设计多合理，他们只看到界面好不好用、卡不卡、丑不丑。.NET生态在UI方面的工具链已经非常完整，做出好看又好用的工控界面，技术不是瓶颈，审美和设计意识才是。

四篇生态地图到这里就完整了：第一篇概览全局，第二篇讲通信协议，第三篇讲数据与业务，第四篇讲展示与交互。从PLC通信到数据存储到界面呈现，一条完整的.NET工控技术栈链路已经画完。

但生态地图只是起点。工控领域有很多值得深入聊的概念——实时性到底是什么意思、工控网络和企业IT网络有什么区别、PLC的扫描周期怎么影响上位机设计、SCADA和MES的边界在哪里。这些概念看起来"虚"，但直接影响你做架构决策时的判断力。接下来的文章我会继续围绕工控本身展开，把这些概念讲透。诸君共勉。

您的点赞和在看是我创作的最大动力，感谢支持

公众号：wacky的碎碎念

知乎：wacky
