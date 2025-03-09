---
title: (6)从护士到C#开发者--基础进阶：程序控制与数据结构
slug: from-nurse-to-csharp-developer-my-learning-journey-6
description: 在C#编程学习的第六天，我学习了循环控制、三元表达式、常量、枚举、结构、数组以及方法等内容。作为一名从护理行业转行的开发者，我将这些编程概念与护理工作经验相结合，帮助自己更好地理解和记忆。
date: 2025-03-05 10:01:14
lastmod: 2025-03-09 20:49:21
copyright: Contributes
author: 勇敢的天使
draft: False
cover: https://img1.dotnet9.com/2025/02/cover_02.png
categories:
  - 分享
  - 编程学习
albums:
  - 从护士到C#开发者
tags:
  - 护士
  - 编程
  - C#
  - 程序控制
  - 数据结构
---

## 前言

在我从护士转行成为 C#开发者的学习旅程中，这是我的第六篇学习笔记。今天我学习了更多关于程序控制和数据结构的内容，这些知识帮助我更好地组织和管理程序中的数据和流程。

## Continue 语句

Continue 语句用于立即结束本次循环，判断循环条件，如果成立，则进入下一次循环，否则退出循环。这让我想到在护理工作中，当某个步骤不适用于特定病人时，我们会跳过该步骤继续执行护理计划的其他部分。

让我通过一个例子来说明 Continue 的使用：

```csharp
// 模拟病房巡视，跳过空床位
for (int bedNumber = 1; bedNumber <= 10; bedNumber++)
{
    // 假设4号床和7号床是空床位
    if (bedNumber == 4 || bedNumber == 7)
    {
        Console.WriteLine($"{bedNumber}号床是空床位，跳过检查");
        continue; // 跳过本次循环，直接进入下一次
    }

    // 执行病人检查流程
    Console.WriteLine($"正在检查{bedNumber}号床的病人");
    // 其他检查代码...
}
```

在这个例子中，循环遍历了 1 到 10 号床位，当遇到空床位（4 号和 7 号）时，使用 continue 语句跳过当前循环，不执行后面的病人检查流程，直接进入下一个床位的检查。

另一个例子是处理患者数据：

```csharp
// 处理患者体温数据，跳过无效数据
double[] temperatures = { 36.5, 37.2, 0, 36.8, 39.1, 0, 36.9 };
double sum = 0;
int validCount = 0;

for (int i = 0; i < temperatures.Length; i++)
{
    // 跳过记录为0的无效数据
    if (temperatures[i] == 0)
    {
        Console.WriteLine($"第{i+1}个体温数据无效，跳过");
        continue;
    }

    // 处理有效数据
    sum += temperatures[i];
    validCount++;
    Console.WriteLine($"记录有效体温: {temperatures[i]}°C");
}

// 计算平均体温
double average = sum / validCount;
Console.WriteLine($"患者平均体温: {average:F1}°C");
```

在护理工作中，continue 语句就像是我们在执行一系列标准护理操作时，根据患者具体情况决定跳过某些不适用的步骤，直接进入下一个环节的过程。这种灵活性使我们能够更高效地处理各种情况。

## 三元表达式

三元表达式的语法为：`表达式1 ? 表达式2 : 表达式3`

- 表达式 1 一般为关系表达式
- 如果表达式 1 的值为 true，那么表达式 2 的值就是整个三元表达式的值
- 如果表达式 1 的值为 false，那么表达式 3 的值就是整个三元表达式的值

需要注意的是，表达式 2 的结果类型必须跟表达式 3 的结果类型一致，并且也要跟整个三元表达式的结果类型一致。

让我通过几个例子来说明三元表达式的使用：

```csharp
// 例1：根据体温判断是否发热
double temperature = 37.8;
string result = temperature >= 37.5 ? "患者发热" : "体温正常";
Console.WriteLine(result); // 输出：患者发热
```

上面的例子等同于以下 if-else 语句：

```csharp
double temperature = 37.8;
string result;
if (temperature >= 37.5)
{
    result = "患者发热";
}
else
{
    result = "体温正常";
}
Console.WriteLine(result);
```

可以看到，三元表达式让代码更简洁。

再看一个稍复杂的例子：

```csharp
// 例2：根据血压值返回不同的护理建议
int systolicBP = 145; // 收缩压
string advice = systolicBP >= 140 ?
                (systolicBP >= 180 ? "紧急处理，立即降压" : "需观察，按医嘱用药") :
                "血压正常，继续监测";
Console.WriteLine(advice); // 输出：需观察，按医嘱用药
```

这个例子中，我们使用嵌套的三元表达式来处理多个条件。当收缩压超过 140 时，再进一步判断是否达到 180 以上的紧急状态。

三元表达式在计算值时也很有用：

```csharp
// 例3：计算药物剂量（根据体重）
double weight = 65.5; // 患者体重（kg）
double baseDose = 5.0; // 基础剂量（mg）
double doseMultiplier = weight < 50 ? 0.8 : (weight > 80 ? 1.2 : 1.0);
double finalDose = baseDose * doseMultiplier;

Console.WriteLine($"患者体重: {weight}kg");
Console.WriteLine($"剂量调整系数: {doseMultiplier}");
Console.WriteLine($"最终药物剂量: {finalDose}mg");
```

在我的护理工作中，类似的快速判断场景很常见。比如根据病人的各项指标分配不同的护理等级，或者根据评估结果决定采取何种措施。三元表达式可以帮助我在代码中简洁地表达这些决策逻辑。

凡是 if-else 可以做的事都可以用三元表达式完成，不过当逻辑过于复杂时，为了代码可读性，使用传统的 if-else 可能更为清晰。

## 常量

常量的声明语法：

```csharp
const 变量类型 变量名 = 值
```

常量一旦声明就不能被重新赋值。在护理中，这类似于一些固定的医疗参数，如正常体温范围、血压标准值等。

让我通过几个例子来说明常量的使用：

```csharp
// 定义医疗相关常量
const double NORMAL_BODY_TEMP = 36.5;      // 正常体温参考值
const int NORMAL_SYSTOLIC_BP = 120;        // 正常收缩压参考值
const int NORMAL_DIASTOLIC_BP = 80;        // 正常舒张压参考值
const int MIN_ADULT_HEART_RATE = 60;       // 成人最低心率
const int MAX_ADULT_HEART_RATE = 100;      // 成人最高心率
const string HOSPITAL_NAME = "仁爱医院";    // 医院名称

// 使用常量
double patientTemp = 38.2;
if (patientTemp > NORMAL_BODY_TEMP + 1.0)
{
    Console.WriteLine("患者体温明显升高，需要医生评估");
}

int patientSystolicBP = 135;
if (patientSystolicBP > NORMAL_SYSTOLIC_BP)
{
    Console.WriteLine("患者血压偏高");
}

Console.WriteLine($"欢迎来到{HOSPITAL_NAME}!");
```

常量的命名通常采用全大写字母，多个单词之间用下划线分隔，这是一种常见的命名约定。

常量的好处：

1. 提高代码可读性 - 使用有意义的名称代替魔法数字（如 36.5 代替体温）
2. 易于维护 - 如果需要修改某个值，只需修改一处常量定义
3. 避免错误 - 防止在代码中意外修改这些值

尝试给常量重新赋值会导致编译错误：

```csharp
const int ADMISSION_AGE = 18;
ADMISSION_AGE = 16; // 编译错误：无法给常量赋值
```

在我的护理工作中，有许多固定的标准和阈值，如生命体征的正常范围、药物剂量的上限、治疗的标准流程等。在编程中使用常量让我想起这些医疗标准，帮助我更好地理解和记忆这个概念。

## 枚举

枚举可以规范我们的开发，语法如下：

```csharp
    [public] enum 枚举名
{
    值1,
    值2,
    值3,
    ...
}
```

public 是访问修饰符，表示公开的、公共的，可以在任何地方访问。枚举名要符合 Pascal 命名规范。将枚举声明到命名空间的下面，类的外面，表示这个命名空间下所有类都可以使用这个枚举。

枚举本质上是一个变量类型，类似于 int、double、string、decimal 等，只是枚举声明、赋值、使用的方式与普通变量类型不同。我们可以将枚举类型的变量与 int 类型和 string 类型互相转换。

让我通过一个医疗场景的例子来说明枚举的使用：

```csharp
// 定义护理评估中的疼痛等级枚举
public enum PainLevel
{
    None,           // 0 - 无疼痛
    Mild,           // 1 - 轻度疼痛
    Moderate,       // 2 - 中度疼痛
    Severe,         // 3 - 严重疼痛
    Unbearable      // 4 - 难以忍受的疼痛
}

// 定义护理任务优先级枚举
public enum NursingPriority
{
    Routine = 1,    // 常规护理
    Urgent = 2,     // 紧急护理
    Emergency = 3   // 危急情况
}

// 定义患者风险评估结果枚举
public enum RiskAssessment
{
    Low = 10,       // 低风险
    Medium = 20,    // 中等风险
    High = 30       // 高风险
}

// 在程序中使用枚举
class Program
{
    static void Main(string[] args)
    {
        // 1. 基本枚举使用
        PainLevel patientPain = PainLevel.Moderate;
        Console.WriteLine($"患者疼痛等级: {patientPain}"); // 输出: 患者疼痛等级: Moderate

        // 2. 根据疼痛等级确定护理优先级
        NursingPriority priority;
        if (patientPain >= PainLevel.Severe)
        {
            priority = NursingPriority.Urgent;
        }
        else if (patientPain == PainLevel.None)
        {
            priority = NursingPriority.Routine;
        }
        else
        {
            priority = NursingPriority.Routine;
        }

        Console.WriteLine($"护理优先级: {priority}"); // 输出: 护理优先级: Routine

        // 3. 使用枚举进行决策
        switch (patientPain)
        {
            case PainLevel.None:
                Console.WriteLine("无需止痛药物");
                break;
            case PainLevel.Mild:
                Console.WriteLine("考虑使用非处方止痛药");
                break;
            case PainLevel.Moderate:
                Console.WriteLine("按医嘱给予口服止痛药");
                break;
            case PainLevel.Severe:
            case PainLevel.Unbearable:
                Console.WriteLine("立即联系医生，考虑注射止痛药");
                break;
            default:
                Console.WriteLine("无效的疼痛评估");
                break;
        }

        // 4. 枚举与整数互相转换
        int painValue = (int)patientPain;
        Console.WriteLine($"疼痛数值: {painValue}"); // 输出: 疼痛数值: 2

        // 整数转换为枚举
        PainLevel convertedPain = (PainLevel)3;
        Console.WriteLine($"转换后的疼痛等级: {convertedPain}"); // 输出: 转换后的疼痛等级: Severe

        // 5. 枚举转换为字符串
        string painString = patientPain.ToString();
        Console.WriteLine($"疼痛描述: {painString}"); // 输出: 疼痛描述: Moderate

        // 6. 字符串转换为枚举
        PainLevel parsedPain = (PainLevel)Enum.Parse(typeof(PainLevel), "Mild");
        Console.WriteLine($"解析后的疼痛等级: {parsedPain}"); // 输出: 解析后的疼痛等级: Mild

        // 7. 尝试转换数字字符串
        PainLevel numericPain = (PainLevel)Enum.Parse(typeof(PainLevel), "1");
        Console.WriteLine($"数字解析的疼痛等级: {numericPain}"); // 输出: 数字解析的疼痛等级: Mild

        // 8. 处理可能发生的异常
        try
        {
            PainLevel invalidPain = (PainLevel)Enum.Parse(typeof(PainLevel), "Critical");
            Console.WriteLine($"解析结果: {invalidPain}");
        }
        catch (ArgumentException)
        {
            Console.WriteLine("无法将'Critical'转换为有效的疼痛等级，因为枚举中没有此值");
        }

        // 9. 获取枚举的所有可能值
        Console.WriteLine("所有可能的疼痛等级:");
        foreach (PainLevel level in Enum.GetValues(typeof(PainLevel)))
        {
            Console.WriteLine($"- {level} ({(int)level})");
        }
    }
}
```

在这个示例中，我们可以看到：

1. **枚举定义**：我们定义了三个与护理相关的枚举类型（疼痛等级、护理优先级和风险评估）
2. **默认值**：如果不指定值，枚举的第一个成员值为 0，后续成员值依次递增 1
3. **自定义值**：我们可以为枚举成员指定自定义的整数值，如`NursingPriority`和`RiskAssessment`
4. **枚举比较**：可以用大于、等于等运算符比较同一枚举类型的不同值
5. **类型转换**：枚举可以与 int 和 string 互相转换
6. **错误处理**：当尝试将不存在的字符串转换为枚举值时会抛出异常

在我的护理工作中，类似的分级系统随处可见。例如：

- 疼痛评估量表（0-10 分）
- 压疮风险评分（Braden 量表）
- 跌倒风险等级
- 静脉炎分级
- 药物分类系统

使用枚举可以让代码更加规范和易于理解，避免使用"魔法数字"（如直接使用 1、2、3 而不说明其含义）。同时，编译器还会检查类型安全，防止我们误用枚举值。

## 结构

结构可以帮助我们一次声明多个不同类型的变量，通常写在命名空间下面。语法如下：

```csharp
[public] struct 结构名（符合Pascal）
{
    成员; //字段
}
```

与变量在程序运行期间只能存一个值不同，结构可以存储多个值。让我通过一个护理场景的例子来说明结构的使用：

```csharp
// 定义患者生命体征结构
public struct VitalSigns
{
    public double Temperature;      // 体温
    public int HeartRate;          // 心率
    public int SystolicBP;         // 收缩压
    public int DiastolicBP;        // 舒张压
    public int RespiratoryRate;    // 呼吸频率
    public int OxygenSaturation;   // 血氧饱和度
}

// 定义患者基本信息结构
public struct PatientInfo
{
    public string Name;            // 姓名
    public int Age;                // 年龄
    public char Gender;            // 性别（'M' 或 'F'）
    public string ID;              // 病历号
    public string Ward;            // 病区
    public int BedNumber;          // 床号
    public VitalSigns Vitals;      // 生命体征
}

// 使用结构的示例
class Program
{
    static void Main(string[] args)
    {
        // 创建并初始化一个患者信息
        PatientInfo patient;
        patient.Name = "张三";
        patient.Age = 45;
        patient.Gender = 'M';
        patient.ID = "P20250305001";
        patient.Ward = "内科";
        patient.BedNumber = 12;

        // 设置患者生命体征
        patient.Vitals.Temperature = 37.2;
        patient.Vitals.HeartRate = 78;
        patient.Vitals.SystolicBP = 130;
        patient.Vitals.DiastolicBP = 85;
        patient.Vitals.RespiratoryRate = 16;
        patient.Vitals.OxygenSaturation = 98;

        // 显示患者信息
        DisplayPatientInfo(patient);

        // 评估生命体征
        AssessVitalSigns(patient.Vitals);
    }

    // 显示患者信息的方法
    static void DisplayPatientInfo(PatientInfo patient)
    {
        Console.WriteLine("患者基本信息：");
        Console.WriteLine($"姓名: {patient.Name}");
        Console.WriteLine($"年龄: {patient.Age}岁");
        Console.WriteLine($"性别: {(patient.Gender == 'M' ? "男" : "女")}");
        Console.WriteLine($"病历号: {patient.ID}");
        Console.WriteLine($"病区: {patient.Ward}");
        Console.WriteLine($"床号: {patient.BedNumber}号");
        Console.WriteLine("\n生命体征：");
        Console.WriteLine($"体温: {patient.Vitals.Temperature}°C");
        Console.WriteLine($"心率: {patient.Vitals.HeartRate}次/分");
        Console.WriteLine($"血压: {patient.Vitals.SystolicBP}/{patient.Vitals.DiastolicBP} mmHg");
        Console.WriteLine($"呼吸: {patient.Vitals.RespiratoryRate}次/分");
        Console.WriteLine($"血氧: {patient.Vitals.OxygenSaturation}%");
    }

    // 评估生命体征的方法
    static void AssessVitalSigns(VitalSigns vitals)
    {
        Console.WriteLine("\n生命体征评估：");

        // 评估体温
        if (vitals.Temperature > 37.5)
            Console.WriteLine("- 体温偏高，需要监测");

        // 评估心率
        if (vitals.HeartRate < 60 || vitals.HeartRate > 100)
            Console.WriteLine("- 心率异常，需要关注");

        // 评估血压
        if (vitals.SystolicBP >= 140 || vitals.DiastolicBP >= 90)
            Console.WriteLine("- 血压偏高，建议复查");

        // 评估呼吸
        if (vitals.RespiratoryRate < 12 || vitals.RespiratoryRate > 20)
            Console.WriteLine("- 呼吸频率异常，需要观察");

        // 评估血氧
        if (vitals.OxygenSaturation < 95)
            Console.WriteLine("- 血氧偏低，考虑给氧");
    }
}
```

在这个例子中，我们可以看到：

1. **结构的定义**：我们定义了两个结构（`VitalSigns`和`PatientInfo`），用于存储患者的相关信息
2. **结构的嵌套**：`PatientInfo`结构中包含了`VitalSigns`结构，展示了结构的嵌套使用
3. **结构的初始化**：展示了如何创建结构变量并设置其字段值
4. **结构作为参数**：展示了如何将结构作为方法参数传递
5. **结构的实际应用**：通过评估生命体征的方法展示了结构在实际场景中的应用

这种组织数据的方式让我想起护理工作中的病历记录系统。在病历中，我们也会将患者的各种信息（基本信息、生命体征、检查结果等）按照特定的格式组织在一起。使用结构可以帮助我们更好地管理这些相关的数据，使代码更有条理和可维护性。

## 数组

数组允许我们一次性存储多个相同类型的变量。语法：

```csharp
数组类型[] 数组名 = new 数组类型[数组长度]
```

例如：`int[] nums = new int[10];`

当我写了上面这行代码后，就在内存中开辟了连续的 10 块空间。我们将每一块空间称为数组的元素。如果想要访问数组中的某个元素，需要通过元素的下标或索引去访问。数组中最后一个元素的索引是长度减一。

让我通过几个护理工作中的例子来说明数组的使用：

```csharp
// 例1：记录一周的病房人数
int[] wardPatients = new int[7];  // 创建长度为7的数组

// 设置每天的病人数量
wardPatients[0] = 45;  // 星期一
wardPatients[1] = 42;  // 星期二
wardPatients[2] = 48;  // 星期三
wardPatients[3] = 50;  // 星期四
wardPatients[4] = 47;  // 星期五
wardPatients[5] = 40;  // 星期六
wardPatients[6] = 38;  // 星期日

// 计算平均住院人数
int totalPatients = 0;
for (int i = 0; i < wardPatients.Length; i++)
{
    totalPatients += wardPatients[i];
}
double averagePatients = (double)totalPatients / wardPatients.Length;
Console.WriteLine($"本周平均住院人数: {averagePatients:F1}人");

// 例2：数组的初始化方式
// 方式1：直接初始化
double[] temperatures = { 36.5, 36.8, 37.2, 36.9, 37.0 };

// 方式2：new关键字初始化
string[] medications = new string[] { "青霉素", "头孢", "阿莫西林", "布洛芬" };

// 方式3：先声明后赋值
int[] bedNumbers;
bedNumbers = new int[] { 101, 102, 103, 104, 105 };

// 例3：二维数组 - 记录多个病人一天的体温记录
double[,] patientTemps = new double[3, 4];  // 3个病人，每天4次体温记录

// 设置体温数据
patientTemps[0, 0] = 36.5;  // 1号病人早上
patientTemps[0, 1] = 36.8;  // 1号病人中午
patientTemps[0, 2] = 37.1;  // 1号病人下午
patientTemps[0, 3] = 36.9;  // 1号病人晚上

// 遍历二维数组
string[] timeSlots = { "早上", "中午", "下午", "晚上" };
for (int patient = 0; patient < 3; patient++)
{
    Console.WriteLine($"\n{patient + 1}号病人的体温记录：");
    for (int time = 0; time < 4; time++)
    {
        Console.WriteLine($"{timeSlots[time]}: {patientTemps[patient, time]}°C");
    }
}

// 例4：数组作为方法参数
static double CalculateAverageTemp(double[] temps)
{
    double sum = 0;
    foreach (double temp in temps)
    {
        sum += temp;
    }
    return sum / temps.Length;
}

// 调用方法
double[] patient1Temps = { 36.5, 36.8, 37.2, 36.9 };
double avgTemp = CalculateAverageTemp(patient1Temps);
Console.WriteLine($"患者平均体温: {avgTemp:F1}°C");

// 例5：数组的常见操作
string[] nurses = { "张护士", "李护士", "王护士", "赵护士" };

// 获取数组长度
Console.WriteLine($"护士数量: {nurses.Length}");

// 查找元素
string searchNurse = "李护士";
bool found = Array.IndexOf(nurses, searchNurse) != -1;
Console.WriteLine($"是否找到{searchNurse}: {found}");

// 数组排序
Array.Sort(nurses);
Console.WriteLine("排序后的护士名单：");
foreach (string nurse in nurses)
{
    Console.WriteLine(nurse);
}

// 数组复制
string[] backupNurses = new string[nurses.Length];
Array.Copy(nurses, backupNurses, nurses.Length);
```

在这些例子中，我们可以看到：

1. **数组的创建**：可以使用多种方式创建和初始化数组
2. **数组的访问**：通过索引访问数组元素
3. **数组的遍历**：使用 for 循环或 foreach 循环遍历数组
4. **二维数组**：用于存储更复杂的数据结构
5. **数组作为参数**：数组可以作为方法的参数传递
6. **数组的常用操作**：如获取长度、查找元素、排序和复制等

数组的长度一旦固定就不能被改变。这让我想到医院中的药品分发系统，每个药品有固定的存储位置和数量。在护理工作中，我们经常需要处理一组相关的数据，比如：

- 病房的床位号
- 患者的体温记录
- 药品清单
- 护理班次安排
- 生命体征监测数据

使用数组可以有效地组织和管理这些数据，使我们的程序更加结构化和易于维护。

## 冒泡排序

冒泡排序用于将数组中的元素按照从大到小或从小到大的顺序排列。例如，对于数组：

```csharp
int[] nums = {9, 8, 7, 6, 5, 4, 3, 2, 1, 0};
```

下面是一个完整的冒泡排序示例，将患者按照优先级排序：

```csharp
// 患者优先级数组（数字越大优先级越高）
int[] priorities = { 2, 5, 1, 7, 3, 8, 4, 6 };
string[] patientNames = { "张三", "李四", "王五", "赵六", "钱七", "孙八", "周九", "吴十" };

Console.WriteLine("排序前的患者优先级:");
for (int i = 0; i < priorities.Length; i++)
{
    Console.WriteLine($"{patientNames[i]}: 优先级 {priorities[i]}");
}

// 冒泡排序（从高到低）
for (int i = 0; i < priorities.Length - 1; i++)
{
    for (int j = 0; j < priorities.Length - 1 - i; j++)
    {
        // 如果当前元素小于下一个元素，交换位置（降序排列）
        if (priorities[j] < priorities[j + 1])
        {
            // 交换优先级
            int tempPriority = priorities[j];
            priorities[j] = priorities[j + 1];
            priorities[j + 1] = tempPriority;

            // 同时交换对应的患者姓名
            string tempName = patientNames[j];
            patientNames[j] = patientNames[j + 1];
            patientNames[j + 1] = tempName;
        }
    }
}

Console.WriteLine("\n排序后的患者优先级（从高到低）:");
for (int i = 0; i < priorities.Length; i++)
{
    Console.WriteLine($"{i+1}. {patientNames[i]}: 优先级 {priorities[i]}");
}
```

这类似于在护理工作中按照优先级对患者进行分诊和治疗。冒泡排序的思想让我联想到护士站中根据患者病情轻重决定先处理哪些患者的场景。

## 方法

方法是将一堆代码进行重用的机制。语法如下：

```csharp
[public] static 返回值类型 方法名([参数列表])
{
    方法体;
}
```

让我通过几个护理相关的例子来说明方法的使用：

```csharp
class NursingProgram
{
    // 1. 无参数无返回值的方法
    public static void DisplayNursingProtocol()
    {
        Console.WriteLine("标准护理流程：");
        Console.WriteLine("1. 测量生命体征");
        Console.WriteLine("2. 检查输液情况");
        Console.WriteLine("3. 观察病情变化");
        Console.WriteLine("4. 记录护理记录");
    }

    // 2. 有参数有返回值的方法
    public static double CalculateIdealWeight(double height, char gender)
    {
        // 根据身高和性别计算理想体重
        if (gender == 'M' || gender == 'm')
        {
            return (height - 100) * 0.9;
        }
        else
        {
            return (height - 100) * 0.85;
        }
    }

    // 3. 多参数的方法
    public static double CalculateBMI(double weight, double height)
    {
        double heightInMeters = height / 100;
        return weight / (heightInMeters * heightInMeters);
    }

    // 4. 方法重载 - 评估体温
    public static string EvaluateTemperature(double temperature)
    {
        if (temperature < 35.5) return "低温";
        if (temperature <= 37.2) return "正常";
        if (temperature <= 38.0) return "低热";
        if (temperature <= 39.0) return "中度发热";
        return "高热";
    }

    // 同名方法，但参数不同
    public static string EvaluateTemperature(double temperature, bool isInfant)
    {
        if (isInfant)
        {
            // 婴儿体温标准略有不同
            if (temperature < 36.0) return "婴儿低温";
            if (temperature <= 37.5) return "婴儿正常体温";
            if (temperature <= 38.5) return "婴儿低热";
            return "婴儿高热";
        }
        else
        {
            return EvaluateTemperature(temperature); // 调用另一个重载方法
        }
    }

    // 5. 参数默认值
    public static string AssessBloodPressure(int systolic, int diastolic, bool detailed = false)
    {
        string result = "";
        if (systolic >= 140 || diastolic >= 90)
        {
            result = "血压偏高";
            if (detailed)
            {
                result += $" (收缩压:{systolic}, 舒张压:{diastolic})";
            }
        }
        else if (systolic < 90 || diastolic < 60)
        {
            result = "血压偏低";
            if (detailed)
            {
                result += $" (收缩压:{systolic}, 舒张压:{diastolic})";
            }
        }
        else
        {
            result = "血压正常";
        }
        return result;
    }

    // 6. 使用ref参数的方法
    public static void UpdateVitalSigns(ref double temperature, ref int pulse)
    {
        // 模拟新的测量值
        temperature = 37.2;
        pulse = 75;
    }

    // 主方法中使用这些方法
    static void Main(string[] args)
    {
        // 调用无参数方法
        DisplayNursingProtocol();

        // 计算理想体重
        double height = 170;
        char gender = 'F';
        double idealWeight = CalculateIdealWeight(height, gender);
        Console.WriteLine($"\n身高{height}cm的{(gender == 'F' ? "女" : "男")}性理想体重: {idealWeight:F1}kg");

        // 计算BMI
        double weight = 65;
        double bmi = CalculateBMI(weight, height);
        Console.WriteLine($"BMI指数: {bmi:F1}");

        // 使用方法重载评估体温
        double adultTemp = 38.5;
        double infantTemp = 37.8;
        Console.WriteLine($"\n成人体温{adultTemp}°C评估: {EvaluateTemperature(adultTemp)}");
        Console.WriteLine($"婴儿体温{infantTemp}°C评估: {EvaluateTemperature(infantTemp, true)}");

        // 使用默认参数
        int systolic = 145;
        int diastolic = 88;
        Console.WriteLine($"\n血压评估: {AssessBloodPressure(systolic, diastolic)}");
        Console.WriteLine($"血压评估(详细): {AssessBloodPressure(systolic, diastolic, true)}");

        // 使用ref参数
        double temp = 36.8;
        int pulse = 80;
        Console.WriteLine($"\n更新前 - 体温: {temp}°C, 脉搏: {pulse}次/分");
        UpdateVitalSigns(ref temp, ref pulse);
        Console.WriteLine($"更新后 - 体温: {temp}°C, 脉搏: {pulse}次/分");
    }
}
```

在这些例子中，我们可以看到：

1. **无参数方法**：`DisplayNursingProtocol()`展示了如何定义和使用无参数方法
2. **有参数和返回值的方法**：`CalculateIdealWeight()`和`CalculateBMI()`展示了如何处理输入参数并返回计算结果
3. **方法重载**：`EvaluateTemperature()`展示了如何为同一个方法名提供不同的参数版本
4. **默认参数**：`AssessBloodPressure()`展示了如何使用可选参数
5. **引用参数**：`UpdateVitalSigns()`展示了如何使用 ref 参数在方法中修改变量的值

这些方法让我想起护理工作中的标准操作流程(SOP)：

- 每个护理程序都有明确的输入（如患者状况）和输出（如护理措施）
- 相同的护理程序可能根据患者类型（如成人和儿童）有不同的变体
- 某些护理操作会直接改变患者的状态（如给药）
- 护理评估可以有简单版本和详细版本

通过将方法与护理实践联系起来，我更容易理解和记忆这些编程概念。方法使我们的代码更有组织性，就像标准化的护理流程一样，提高了工作效率和准确性。

## return 语句

return 语句有两个作用：

1. 在方法中返回要返回的值
2. 立即结束本次方法的执行

下面是一个使用 return 语句的例子：

```csharp
// 评估患者体温状态并给出护理建议
static string EvaluateTemperature(double temperature)
{
    // 低温
    if (temperature < 35.5)
    {
        return "体温过低，需保暖并监测";
    }

    // 正常体温
    if (temperature >= 35.5 && temperature <= 37.2)
    {
        return "体温正常，定时监测";
    }

    // 轻度发热
    if (temperature > 37.2 && temperature <= 38.0)
    {
        return "轻度发热，密切观察";
    }

    // 中度发热
    if (temperature > 38.0 && temperature <= 39.0)
    {
        return "中度发热，物理降温并考虑用药";
    }

    // 高热
    return "高热，紧急处理，立即通知医生";
}

// 在Main方法中调用
static void Main(string[] args)
{
    double[] patientTemps = { 36.5, 37.8, 39.2, 35.0, 38.5 };

    foreach (double temp in patientTemps)
    {
        string advice = EvaluateTemperature(temp);
        Console.WriteLine($"体温 {temp}°C: {advice}");
    }
}
```

在这个例子中，一旦满足某个条件，return 语句就会返回相应的建议并立即结束方法的执行，不会继续检查后面的条件。这类似于在护理决策中，一旦确定了患者的情况，就立即采取对应的措施，而不需要考虑其他可能性。

## 总结

作为一名护士转行的开发者，我发现编程和护理工作有许多相似之处。通过将这些编程概念与护理经验联系起来，我能够更好地理解和应用这些知识，逐步成长为一名优秀的 C#开发者。
