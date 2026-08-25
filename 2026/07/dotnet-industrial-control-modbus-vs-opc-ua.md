> 转载声明：本文经原作者 **wackyxk** 授权，由 CodeWF 转载发布。
>
> 原文首发于微信公众号「wacky的碎碎念」，版权归原作者所有。
>
> 原文链接：[阅读原文](https://mp.weixin.qq.com/s/FZ26r2ggcgrs3S1GDQvSKw)
>
> 本文仅作 Markdown 排版与图片本地化处理，未对原文观点作实质性修改。

---

## 前言

大家好，我是wacky。上一篇我们聊了工控的基本概念——什么是PLC、通信协议是干什么的以及.NET在工控领域的生态位。今天我们往下挖一层，进入实战环节：通信协议到底怎么用、怎么选、怎么避坑。

上一篇留了个概览表，今天展开聊两个核心协议：Modbus和OPC UA。为什么是这两个？因为它们覆盖了工业现场绝大多数上位机通信场景。Modbus是通用性最强的"普通话"，OPC UA是面向未来的跨厂商标准。我们以汇川PLC为例来讲解——汇川在国产PLC中市场占有率很高，EVO系列同时支持Modbus TCP和OPC UA，配套的iFA Evolution软件平台功能完善，非常适合作为学习对象。

## Modbus：先学会走，再学跑

如果工控协议有一个"普通话"，那就是Modbus。1979年由Modicon公司发明，至今45年，仍然是工业现场出场率最高的协议。原因很简单：公开、免费、几乎所有PLC都支持——汇川也不例外。

汇川PLC在iFA Evolution软件中可以直接配置Modbus TCP主站或从站。在硬件配置树的LAN1节点下添加"ModbusTCP Master"或"ModbusTCP Slave"设备，配置好通信参数即可。这一步在PLC侧完成，上位机侧只需要通过Modbus协议连接PLC的IP和端口。

.NET社区有现成的库，NuGet安装NModbus4，读取线圈状态的核心代码就几行：

```csharp
IPAddress address = new IPAddress(new byte[] { 127, 0, 0, 1 });
using (TcpClient client = new TcpClient(address.ToString(), 502))
{
    client.SendTimeout = 1;
    ModbusIpMaster master = ModbusIpMaster.CreateIp(client);
    // 读取10个值
    ushort startAddress = 0;
    ushort numInputs = 10;
    bool[] inputs = master.ReadCoils(1, startAddress, numInputs);
    for (int i = 0; i < numInputs; i++)
    {
        Console.WriteLine($"Input {(startAddress + i)}={(inputs[i] ? 1 : 0)}");
    }
}
```

如果没办法用汇川的iFA可以自己下载一个Modbus Slave来调试。

Modbus的数据模型很简单，就四种数据类型：线圈（Coil，布尔量）、离散输入（Discrete Input，只读布尔量）、输入寄存器（Input Register，只读16位整数）、保持寄存器（Holding Register，可读写16位整数）。上位机最常用的是保持寄存器，读写都在这里。

Modbus的好处是通用、简单、资料多，不管你换什么品牌的PLC，Modbus的代码几乎不用改。坏处也明显：只有寄存器读写，没有事件通知机制（你得轮询），不支持复杂数据结构，没有内置安全机制。但对于数据采集和简单监控来说，Modbus完全够用，是入门工控通信的最佳起点。

## OPC UA：为什么说它代表未来

Modbus够用，但不够好。当你的项目变复杂——设备多了、数据类型杂了、安全要求高了——Modbus的局限性就暴露出来了。这时候，OPC UA是一个更好的选择。

OPC UA全称"开放平台通信统一架构"。和Modbus这种"寄存器读写"模式不同，OPC UA定义了一套完整的信息模型：不只是传输数据，还定义了数据的含义、结构和关系。

举一个直观的例子。用Modbus读取温度，你拿到的是一个寄存器值，至于这个值是摄氏度还是华氏度、精度是多少、量程范围多大——Modbus不告诉你，你得靠文档或者口口相传。而OPC UA会把这些元信息都带过来，你的代码读到的不仅是一个数字，还是一个有完整描述的"温度对象"。

汇川PLC对OPC UA的支持相当完善。以EVO系列为例，在iFA Evolution软件平台中开启OPC UA服务端，配置符号导出后，PLC就变成了一个OPC UA服务器，上位机可以通过节点地址直接读写PLC变量。EVO500是适配iFA Evolution的第一款产品，在高精运控、过程控制、产线设备开发场景都有广泛应用。

.NET侧有OPC Foundation官方维护的OPCFoundation.NetStandard.Opc.Ua库，以及封装更友好的OpcUaHelper。连接汇川PLC并读取变量的核心流程如下：

```csharp
// 实例化OPC UA客户端
var client = new OpcUaClient();
// 匿名连接汇川PLC的OPC UA服务端
client.UserIdentity = new UserIdentity(new AnonymousIdentityToken());
await client.ConnectServer("opc.tcp://192.168.1.100:4840");
// 读取PLC变量（节点地址在iFA Evolution符号配置中导出）
// 汇川的节点格式通常是：ns=4;s=|var|设备名.Application.GVL.变量名
DataValue dataValue = client.ReadNode<DataValue>(
 "ns=4;s=|var|Inovace-ARM-Linux.Application.GVL.value1");
float temperature = dataValue.Value != null
 ? Convert.ToSingle(dataValue.Value)
 : 0f;
// 订阅变量变化（不用轮询，数据变化时自动触发回调）
client.AddSubscription("sub1",
 "ns=4;s=|var|Inovace-ARM-Linux.Application.GVL.OutVal",
 (key, item, args) =>
 {
 foreach (var v in item.DequeueValues())
 {
 float newVal = Convert.ToSingle(v.Value);
 Console.WriteLine($"数值更新: {newVal}");
 }
 });
Console.ReadLine();
```

如果没办法用汇川的iFA，可以使用其他模拟软件来替代。

OPC UA的三个核心优势：

第一，跨厂商。汇川、西门子、罗克韦尔、施耐德都支持OPC UA，一套协议对接所有品牌，不用再为每个PLC学一套协议。

第二，内置安全。支持加密传输、数字证书认证、用户权限管理。Modbus是明文传输，在工业网络安全日益重要的今天，这是一个硬伤。

第三，订阅机制。不用轮询了，数据变化时PLC会主动通知你。上面代码里的AddSubscription就是这个作用——变量一变，回调自动触发，比Modbus的轮询模式高效得多。

## 两个协议怎么选

通用性：Modbus几乎所有PLC；OPC UA跨厂商标准（主流品牌均支持）

功能丰富度：Modbus寄存器读写；OPC UA信息模型+订阅+安全

学习成本：Modbus低；OPC UA中高

.NET库成熟度：Modbus用NModbus4，成熟；OPC UA用OPC Foundation SDK + OpcUaHelper

汇川PLC支持：EVO系列两种都支持

适合场景：Modbus适合通用数据采集、简单监控；OPC UA适合新建项目、跨品牌系统、实时监控

性能：Modbus中等（轮询模式）；OPC UA高（订阅模式）

我的建议很简单：入门从Modbus开始——它通用、简单、资料多。进阶转OPC UA——功能强大、跨厂商、安全机制完善。两者不是互斥的，实际项目中经常混用——比如底层设备走Modbus，PLC作为网关转OPC UA对接上位机。

## 没硬件怎么学

这是新手问得最多的问题。工控开发最大的门槛不是代码，是硬件——一台PLC几千到几万块，不是想买就买的。

好消息是，模拟器可以解决大部分学习需求：

学Modbus：用Modbus Slave（Witte Software出品），在PC上模拟一个Modbus从站，你的代码连本机就能跑

学OPC UA：用Unified Automation的UaExpert客户端 + 模拟服务器，完整体验节点浏览、读写、订阅的流程

学汇川PLC：iFA Evolution支持离线仿真，不接真机也能调试PLC程序和通信配置

用模拟器学通信，效果和真机差不多。等你把协议搞通了，再去碰真机，就不会手忙脚乱。

## 三个坑

通信层有三个坑，几乎每个工控新手都会踩：

第一，超时设置。库的默认超时通常只有几秒，工业现场网络波动大，建议设到10-30秒，并加上重试机制。别让一次网络抖动把你的程序搞崩。

第二，断线重连。PLC重启或网络闪断后，你的程序要能自动恢复连接。指数退避策略（1秒→2秒→4秒→最大30秒）是比较稳妥的做法，别一断线就疯狂重连把PLC搞挂。

第三，字节序。这是最隐蔽的坑。Modbus用大端字节序，而x86 PC是小端。用BitConverter解析Modbus数据时如果不做转换，数值会完全错乱。OPC UA在这方面好很多——它自带类型描述，读取时直接返回对应的.NET类型，不用手动拼字节。这也是我推荐进阶用OPC UA的原因之一。

## 后记

通信协议是工控开发的"第一道门"。门内是广阔的数据世界——采集到的数据怎么存、怎么管、怎么用。下一篇我们聊数据与业务层，诸君共勉。

您的点赞和在看是我创作的最大动力，感谢支持

公众号：wacky的碎碎念

知乎：wacky
