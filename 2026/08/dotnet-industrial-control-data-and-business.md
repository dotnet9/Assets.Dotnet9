> 转载声明：本文经原作者 **wackyxk** 授权，由 CodeWF 转载发布。
>
> 原文首发于微信公众号「wacky的碎碎念」，版权归原作者所有。
>
> 原文链接：[阅读原文](https://mp.weixin.qq.com/s/btOyVo1NSUsz8nj0zAaILA)
>
> 本文仅作 Markdown 排版与图片本地化处理，未对原文观点作实质性修改。

---

## 前言

大家好，我是wacky。上一篇我们聊了通信协议——Modbus和OPC UA怎么选、怎么用、怎么避坑。如果通信协议是"把数据搬过来"，那今天这一篇解决的问题是：数据搬过来之后怎么办。

一个工控上位机系统，通信只是第一步。PLC每秒推送几百上千个数据点，你拿到了温度、压力、流量、位置……然后呢？直接显示在界面上？关掉程序就没了。写进文本文件？查询的时候痛苦不堪。更别提那些复杂的业务逻辑——设备运行到什么阶段了、什么时候该报警、报警之后走什么流程——这些都不是通信层能解决的。

这一篇我们聊三件事：数据怎么存、数据怎么访问、业务逻辑怎么设计。对应.NET工控技术栈的第二层——数据与业务层。

## 数据存储：先分清两类数据

工控场景的数据分两类，存储策略完全不同。

第一类是配置数据和业务数据——设备信息、用户权限、工单记录、报警历史。这类数据的特点是结构化、需要事务支持、查询条件复杂、数据量可控。说白了就是关系型数据库的活儿。

第二类是时序数据——传感器采集的温度曲线、电机转速波动、压力趋势。这类数据的特点是写入频繁（每秒成百上千条）、单条数据简单、按时间范围查询、很少修改。这是时序数据库的主场。

两类数据用不同的存储方案，别图省事用一个数据库搞定所有事。

## SQLite：工控上位机的默认选择

配置数据和业务数据，首选必须是SQLite。

原因很实在：工控上位机通常部署在工业PC或触摸屏上，不一定有数据库服务器，也不一定有DBA维护。SQLite是嵌入式数据库，不需要安装服务、不需要配置账号密码、一个文件就是整个数据库，跟着你的程序走。对于大多数单机上位机项目，SQLite完全够用。

EFCore配合SQLite，NuGet安装Microsoft.EntityFrameworkCore.Sqlite，定义好实体类和DbContext就能跑：

```csharp
// 报警记录实体
public class AlarmRecord
{
    public long Id { get; set; }
    public DateTime Timestamp { get; set; }
    public string DeviceName { get; set; }     // 设备名称
    public string AlarmCode { get; set; }       // 报警代码
    public string Description { get; set; }     // 报警描述
    public AlarmLevel Level { get; set; }       // 报警等级
    public bool IsAcknowledged { get; set; }    // 是否已确认
}

public class AlarmLevel
{
    public int Id { get; set; }
    public string Name { get; set; }            // Info / Warning / Critical
}

// DbContext
public class AppDbContext : DbContext
{
    public DbSet<AlarmRecord> Alarms { get; set; }
    public DbSet<AlarmLevel> AlarmLevels { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder options) => options.UseSqlite("Data Source=control.db");
}
```

写入一条报警记录就这么直接：

```csharp
using var db = new AppDbContext();

db.Alarms.Add(new AlarmRecord
{
    Timestamp = DateTime.Now,
    DeviceName = "EVO500-主轴电机",
    AlarmCode = "TEMP_OVER",
    Description = "主轴温度超过80℃",
    Level = new AlarmLevel
    {
        Id = 0,
        Name = "Critical"
    },
    IsAcknowledged = false
});

db.SaveChanges();
```

SQLite有一个工控场景下特别重要的特性：WAL模式（Write-Ahead Logging）。默认模式下，写入会锁住整个数据库，读操作被阻塞。开启WAL后读写可以并发，不会因为正在写入历史数据导致界面查询卡顿。一行配置搞定：

```csharp
 // 数据库初始化后执行：PRAGMA journal_mode=WAL;
    protected override void OnConfiguring(DbContextOptionsBuilder options) => options.UseSqlite("Data Source=control.db;Mode=ReadWriteCreate");
```

## EF Core还是Dapper

这是个绕不开的问题。EF Core和Dapper都能做数据访问，选哪个？

EF Core的优势是开发效率高——实体类定义好，迁移自动生成表结构，LINQ写查询不用拼SQL。对于配置数据、业务数据这种CRUD为主的场景，EF Core很合适。

Dapper的优势是性能高、轻量——它是微型ORM，直接写SQL，映射到对象。对于高频写入的报警记录、批量插入操作日志，Dapper比EF Core快几倍。

我的做法是混用。业务数据的增删改查用EF Core，高频写入和复杂查询用Dapper。两者可以共享同一个数据库连接，互不冲突。

```csharp
// EF Core：查询未确认的报警列表
using var db = new AppDbContext();

var activeAlarms = db.Alarms
  .Where(a => !a.IsAcknowledged)
  .OrderByDescending(a => a.Timestamp)
  .ToList();

// Dapper：批量插入采集日志
using var conn = new SqliteConnection("Data Source=control.db");
var logBatch = new[]
{
    new { Timestamp = DateTime.Now, TagName = "Temperature", Value = 25.6 },
    new { Timestamp = DateTime.Now, TagName = "Pressure", Value = 101.3 },
    new { Timestamp = DateTime.Now, TagName = "Humidity", Value = 60.2 }
};
conn.Execute("INSERT INTO CollectionLogs (Timestamp, TagName, Value) VALUES (@Timestamp, @TagName, @Value)", logBatch);
```

## InfluxDB：时序数据的正确打开方式

说回第二类数据——时序数据。如果你只是存几百个标签位、保留几天，SQLite也能凑合。但一旦数据量大起来——几十个PLC、每个PLC上百个变量、每秒采一次、要保留半年——SQLite就扛不住了。查询一条温度曲线，从几百万行里筛选时间范围，SQLite会让你等到怀疑人生。

这时候就该上时序数据库了。InfluxDB是.NET工控圈用得比较多的方案，专为时序数据设计。它的数据模型和关系型数据库完全不同——不按行存储，按时间序列存储，写入和范围查询都极快。

.NET中可以使用InfluxDB.Client库，写入一段温度采集数据：

```csharp
using var client = InfluxDBClientFactory.Create("http://localhost:8086", "your-token");

// 写入一条温度数据点
var point = PointData.Measurement("temperature")
    .Tag("device", "EVO500")
    .Tag("location", "车间A-工位3")
    .Field("value", 76.5)
    .Timestamp(DateTime.UtcNow, WritePrecision.Ms);

using var writeApi = client.GetWriteApi();
writeApi.WritePoint(point, "industrial", "org");

// 查询最近一小时的温度趋势
var flux = @"
        from(bucket: ""industrial"")
            |> range(start: -1h)
            |> filter(fn: (r) => r._measurement == ""temperature"" and r.device == ""EVO500"")
            |> aggregateWindow(every: 1m, fn: mean)";

var queryApi = client.GetQueryApi();
var tables = await queryApi.QueryAsync(flux, "org");

foreach (var table in tables)
{
    foreach (var record in table.Records)
    {
        Console.WriteLine($"{record.GetTime():HH:mm} -> {record.GetValue()}");
    }
}
```

这段代码做的事是：从InfluxDB中查询EVO500设备最近1小时的温度数据，按1分钟窗口取平均值。在界面上画成曲线，就是一条平滑的趋势图。

时序数据的存储策略有个要点：热数据存InfluxDB，冷数据归档。比如最近3个月的数据放在InfluxDB里供实时查询，超过3个月的自动导出到Parquet文件或压缩归档。别让时序库无限膨胀，否则内存和磁盘都吃不消。

## Stateless：设备状态机没那么难

数据存好了，接下来是业务逻辑。工控场景中最常见的业务逻辑是状态管理——设备现在是什么状态、什么条件触发状态切换、切换时执行什么动作。

很多人一开始用一堆if-else和bool变量来管理状态，设备少的时候能跑，设备一多就变成意大利面条——状态转换逻辑散落在各处，改一个地方怕影响全局，调试的时候根本理不清"设备怎么走到这个异常状态的"。

正确的做法是用状态机。.NET生态里Stateless是最轻量的状态机库，NuGet一键安装，定义状态和转换规则后，状态流转由库托管，你的代码只需要关注业务动作。

以汇川EVO系列PLC控制的加热设备为例，定义四个状态：待机、加热中、保温中、故障：

```csharp
// 状态机配置
var machine = new StateMachine<DeviceState, DeviceTrigger>(DeviceState.Idle);

machine.Configure(DeviceState.Idle)
    .Permit(DeviceTrigger.StartHeat, DeviceState.Heating)
    .Ignore(DeviceTrigger.ReachTarget);

machine.Configure(DeviceState.Heating)
    .OnEntry(() => Console.WriteLine("启动加热器，开始升温"))
    .Permit(DeviceTrigger.ReachTarget, DeviceState.Holding)
    .Permit(DeviceTrigger.FaultOccurred, DeviceState.Fault);

machine.Configure(DeviceState.Holding)
    .OnEntry(() => Console.WriteLine("到达目标温度，切换保温模式"))
    .Permit(DeviceTrigger.Stop, DeviceState.Idle)
    .Permit(DeviceTrigger.FaultOccurred, DeviceState.Fault);

machine.Configure(DeviceState.Fault)
    .OnEntry(() => Console.WriteLine("设备故障！触发报警，切断加热器"))
    .Permit(DeviceTrigger.Reset, DeviceState.Idle);
```

状态机的使用非常直观——收到事件时调用Fire触发转换，Stateless会自动检查当前状态是否允许该转换：

```csharp
double currentTemp = 0;
// 从PLC读到温度到达目标值
if (currentTemp >= 80)
{
    machine.Fire(DeviceTrigger.ReachTarget);
}

// 当前是Heating状态 → 自动切换到Holding，同时触发OnEntry里的"切换保温模式"

bool plcFaultSignal = false;
//发生故障
plcFaultSignal = true;
// PLC上报故障信号
if (plcFaultSignal)
{
    machine.Fire(DeviceTrigger.FaultOccurred);
}

// 无论当前什么状态（只要不是Fault）→ 切换到Fault，触发报警逻辑
```

Stateless最大的价值不是少写了几行if-else，而是把状态转换规则集中在一个地方定义，看得见、改得动、测得了。新人接手项目，看一眼状态机配置就能理解设备的行为逻辑，不用满代码库翻找"这个状态是在哪儿切换的"。

## Elsa：当业务流程复杂到状态机扛不住

Stateless适合单设备的状态管理。但有些工控场景的流程更复杂——不只是一个设备的状态，而是跨设备、跨系统、有审批节点的业务流程。比如：报警触发后，先通知值班工程师→15分钟未确认则升级通知车间主管→主管确认后生成维修工单→工单完成后设备复位。这种多步骤、有条件分支、有超时升级的流程，用状态机写会很别扭，用if-else更是灾难。

这时候可以考虑工作流引擎。.NET生态里Elsa是比较活跃的工作流框架，支持可视化设计流程、持久化流程状态、超时和事件触发。不过说实话，Elsa在工控领域的应用还不算普及，大多数项目用Stateless + 简单的定时器就能覆盖。Elsa更适合那些业务流程复杂到需要"流程图"来管理的场景。

这一层只先点到为止，实际项目中你会发现——80%的业务逻辑用Stateless就够了，Elsa还是太庞大了。

## 三层架构怎么落地

把前面聊的串起来，一个典型的.NET工控上位机数据与业务层架构是这样的：

![工控数据与业务层架构](https://img1.dotnet9.com/2026/08/dotnet-industrial-control-data-business-architecture.png)

最底层是数据访问层——EF Core管配置数据和业务数据的CRUD，Dapper管高频写入和复杂查询，InfluxDB管时序数据。三个各司其职，互不越界。

中间层是业务逻辑层——Stateless管理设备状态机，报警引擎判断阈值和升级规则，定时任务做数据归档和报表生成。

最上面是服务层——把业务逻辑封装成接口，供展示层调用。展示层不需要知道数据存在哪、状态机怎么转，只管调接口拿结果。

这个分层不是教条，而是实战经验。我见过太多上位机项目把通信、存储、业务逻辑、界面全搅在一起，前期开发快，后期改一个功能牵一发动全身。分层的好处是每一层可以独立测试、独立替换——换通信协议不影响业务逻辑，换数据库不影响界面，换界面框架不影响数据层。

## 后记

数据与业务层是工控上位机的"中场"——它承接通信层搬来的数据，支撑展示层的所有交互。这一层做不好，界面再漂亮也是空中楼阁；这一层做扎实了，上面换什么界面框架都从容。

目前三篇工控生态地图到这里就完整了：第一篇概览全局，第二篇讲通信协议，第三篇讲数据与业务。下一篇我们进入展示层——界面框架怎么选、可视化控件怎么用、怎么让工业软件告别"土味"。敬请期待！

您的点赞和在看是我创作的最大动力，感谢支持

公众号：wacky的碎碎念

知乎：wacky
