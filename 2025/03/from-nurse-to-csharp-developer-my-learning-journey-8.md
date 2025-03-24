---
title: (8)从护士到C#开发者--数据类型与继承
slug: from-nurse-to-csharp-developer-my-learning-journey-8
description: 本文将结合医护工作场景，详细讲解C#中的命名空间、数据类型、字符串处理、继承以及集合等重要概念，帮助医护人员更好地理解编程知识。
date: 2025-03-20 09:12:26
lastmod: 2025-03-24 21:47:14
copyright: Contributes
author: 勇敢的天使
draft: false
cover: https://img1.dotnet9.com/2025/02/cover_02.png
categories:
  - 分享
  - 编程学习
  - 面向对象编程
albums:
  - 从护士到C#开发者
tags:
  - 护士
  - 编程
  - C#
  - 数据类型
  - 继承
  - 集合
---

作为一名从护理行业转行到编程领域的学习者，我发现很多编程概念都可以通过医护工作经验来理解。本文将结合医院信息系统的实际场景，详细讲解 C#中的几个重要概念。

## 1. 命名空间

命名空间是一种组织和管理代码的方式，就像医院的组织架构一样。比如医院分为内科、外科、护理部等不同部门，每个部门都有自己的职责和管理范围。在 C#中，命名空间也起到类似的作用，它可以：

1. 避免命名冲突（就像医院里不同科室可以有相同名字的护士）
2. 提供逻辑分组（像医院的科室划分）
3. 控制代码的访问范围（类似于医院的权限管理）

### 1.1 命名空间的组织结构

```csharp
namespace HospitalSystem          // 整个医院系统
{
    namespace Administration     // 行政管理
    {
        // 人事管理、财务管理等类
    }

    namespace Clinical          // 临床医疗
    {
        namespace Internal      // 内科
        {
            // 内科相关类
        }

        namespace Surgery       // 外科
        {
            // 外科相关类
        }
    }

    namespace Nursing          // 护理部门
    {
        namespace WardManagement        // 病房管理
        {
            // 病房相关的类
        }

        namespace MedicationManagement  // 用药管理
        {
            // 用药相关的类
        }
    }
}
```

### 1.2 命名空间的使用方法

1. 可以认为类是属于命名空间的
2. 如果在当前项目中没有这个类的命名空间，需要我们手动导入这个类所在的命名空间：
   - 使用鼠标点击 Visual Studio 提示
   - 使用快捷键 alt+shift+F10
   - 记住常用命名空间，手动输入

### 1.3 在项目中使用命名空间

```csharp
// 方法1：使用using导入
using HospitalSystem.Nursing;
using HospitalSystem.Pharmacy;

// 方法2：使用完整限定名称
HospitalSystem.Nursing.NursingRecord record = new HospitalSystem.Nursing.NursingRecord();

// 方法3：使用别名
using NurseRecord = HospitalSystem.Nursing.NursingRecord;
```

### 1.4 项目引用示例

在一个项目中引用另一个类：

1. 添加引用（如添加 HospitalCore.dll）
2. 引用命名空间

```csharp
using HospitalCore.Models;
using HospitalCore.Services;
```

## 2. 值类型和引用类型

### 2.1 值类型详解

值类型直接存储数据本身，包括：

1. 整数类型

   - sbyte: 8 位有符号整数 (-128 到 127)
   - byte: 8 位无符号整数 (0 到 255)
   - short: 16 位有符号整数
   - ushort: 16 位无符号整数
   - int: 32 位有符号整数（最常用）
   - uint: 32 位无符号整数
   - long: 64 位有符号整数
   - ulong: 64 位无符号整数

2. 浮点类型

   - float: 32 位单精度浮点数（精确到 6-9 位）
   - double: 64 位双精度浮点数（精确到 15-17 位）
   - decimal: 128 位高精度小数（常用于财务计算）

3. 其他值类型
   - bool: 布尔值（true/false）
   - char: 16 位 Unicode 字符
   - enum: 枚举类型
   - struct: 结构体

在医疗系统中的应用示例：

```csharp
public struct VitalSigns
{
    public int HeartRate;        // 心率，范围通常60-100
    public decimal Temperature;   // 体温，精确到小数点后一位，如36.5
    public int BloodPressureHigh; // 收缩压，如120
    public int BloodPressureLow;  // 舒张压，如80
    public bool IsFever;          // 是否发烧

    // 枚举示例
    public enum TemperatureMethod
    {
        Oral,      // 口温
        Axillary,  // 腋温
        Rectal     // 肛温
    }
    public TemperatureMethod Method { get; set; }

    // 值类型的数据验证方法
    public bool IsNormal()
    {
        return HeartRate >= 60 && HeartRate <= 100
            && Temperature >= 36.0m && Temperature <= 37.2m
            && BloodPressureHigh >= 90 && BloodPressureHigh <= 140
            && BloodPressureLow >= 60 && BloodPressureLow <= 90;
    }
}
```

### 2.2 引用类型详解

引用类型包括：

1. 类（class）

   - 所有自定义类
   - 系统预定义类（如 String, Object 等）

2. 接口（interface）

3. 委托（delegate）

4. 数组（array）

   - 无论元素是值类型还是引用类型，数组本身都是引用类型

5. 字符串（string）
   - 虽然经常使用，但 string 是引用类型
   - 具有特殊的不可变性质

医疗系统示例：

```csharp
public class PatientRecord
{
    // 基本信息
    public string PatientName { get; set; }
    public string IdNumber { get; set; }
    public DateTime DateOfBirth { get; set; }

    // 诊断信息（数组示例）
    public string[] Diagnoses { get; set; }

    // 用药信息（集合示例）
    public List<Medication> Medications { get; set; }

    // 生命体征记录（自定义类示例）
    public List<VitalSigns> VitalSignsHistory { get; set; }

    // 委托示例 - 用于生命体征异常通知
    public delegate void VitalSignsAlertHandler(string message);
    public event VitalSignsAlertHandler OnVitalSignsAlert;

    // 深拷贝示例
    public PatientRecord Clone()
    {
        var newRecord = new PatientRecord
        {
            PatientName = this.PatientName,
            IdNumber = this.IdNumber,
            DateOfBirth = this.DateOfBirth,
            Diagnoses = (string[])this.Diagnoses.Clone(),
            Medications = this.Medications.Select(m => m.Clone()).ToList(),
            VitalSignsHistory = this.VitalSignsHistory.Select(vs => vs.Clone()).ToList()
        };

        return newRecord;
    }
}

public class Medication
{
    public string Name { get; set; }
    public double Dosage { get; set; }

    // 深拷贝方法
    public Medication Clone()
    {
        return new Medication
        {
            Name = this.Name,
            Dosage = this.Dosage
        };
    }
}

public class VitalSigns
{
    public decimal Temperature { get; set; }
    public int HeartRate { get; set; }

    // 深拷贝方法
    public VitalSigns Clone()
    {
        return new VitalSigns
        {
            Temperature = this.Temperature,
            HeartRate = this.HeartRate
        };
    }
}
```

### 2.3 内存存储区别详解

在 C#中，内存分为栈（Stack）和堆（Heap）两个主要区域：

1. 栈（Stack）

   - 存储值类型的数据
   - 由系统自动管理内存的分配和释放
   - 访问速度快
   - 空间有限
   - 按后进先出（LIFO）的顺序存储

2. 堆（Heap）
   - 存储引用类型的实际数据
   - 需要垃圾回收器（GC）管理内存
   - 空间较大但访问速度相对较慢
   - 内存分配和回收更灵活

示例代码：

```csharp
public class MemoryExample
{
    public void DemonstrateMemoryUsage()
    {
        // 值类型示例
        int temperature = 37;         // 直接在栈上分配4字节
        bool isCritical = true;       // 直接在栈上分配1字节
        DateTime checkTime = DateTime.Now; // 虽然DateTime是struct，但仍在栈上分配

        // 引用类型示例
        string patientName = "张三";   // 在堆上分配字符串数据，栈上存储引用
        PatientRecord record = new PatientRecord(); // 对象在堆上，引用在栈上

        // 值类型复制示例
        int temp2 = temperature;      // 在栈上创建新的独立副本
        temp2 = 38;                   // 修改temp2不影响temperature
        Console.WriteLine($"原温度: {temperature}, 新温度: {temp2}"); // 37, 38

        // 引用类型复制示例
        PatientRecord record2 = record; // 复制引用，两个变量指向同一个堆上的对象
        record2.PatientName = "李四";   // 通过record2修改会影响record
        Console.WriteLine($"record的病人: {record.PatientName}"); // 输出"李四"

        // 字符串特例示例
        string str1 = "测试";
        string str2 = "测试";     // str2和str1指向同一个字符串对象（字符串池）
        string str3 = new string(new char[] { '测', '试' }); // 强制创建新对象

        // 数组示例
        int[] temperatures = new int[] { 36, 37, 38 }; // 数组对象在堆上，元素在数组对象内连续存储
        int[] temps2 = temperatures;  // 复制引用
        temps2[0] = 39;              // 修改temps2同时影响temperatures
    }

    // 值类型作为参数
    public void UpdateTemperature(int temp)
    {
        temp = 39; // 不会影响原始值
    }

    // 引用类型作为参数
    public void UpdatePatient(PatientRecord patient)
    {
        patient.PatientName = "王五"; // 会影响原始对象
    }
}
```

## 3. 字符串处理

字符串在医疗信息系统中使用非常频繁，比如病历记录、医嘱记录等。C#提供了丰富的字符串处理方法：

### 3.1 字符串的特性

1. 不可变性：字符串是不可变的，每次修改都会创建新的字符串对象
2. 字符串池：相同的字符串字面量会共享同一个对象
3. 可以看作是 char 类型的只读数组

### 3.2 常用字符串方法详解

```csharp
public class NursingNoteProcessor
{
    public void ProcessNursingNotes()
    {
        // 护理记录示例
        string note = "  患者张三，男，62岁。\n" +
                     "警告：对青霉素过敏！\n" +
                     "  体温37.2℃，血压120/80mmHg  ";

        // 1. 基本字符串操作
        Console.WriteLine($"记录长度: {note.Length}");  // 获取字符串长度

        // 2. 空白处理
        string trimmed = note.Trim();      // 去除两端空格
        string trimStart = note.TrimStart(); // 去除开头空格
        string trimEnd = note.TrimEnd();    // 去除结尾空格

        // 3. 大小写转换
        string upper = note.ToUpper();     // 转大写（用于重要警告）
        string lower = note.ToLower();     // 转小写

        // 4. 查找操作
        bool hasAllergy = note.Contains("过敏");  // 检查是否包含某字符串
        int allergyIndex = note.IndexOf("过敏");  // 查找第一次出现的位置
        int lastIndex = note.LastIndexOf("，");   // 查找最后一次出现的位置

        // 5. 替换操作
        string replaced = note.Replace("警告", "【警告】");
        string noSpaces = note.Replace(" ", "");

        // 6. 字符串分割
        string[] lines = note.Split(new[] { "\n" }, StringSplitOptions.RemoveEmptyEntries);
        foreach (string line in lines)
        {
            Console.WriteLine($"行内容: {line.Trim()}");
        }

        // 7. 子串提取
        int ageStart = note.IndexOf("，") + 1;
        int ageEnd = note.IndexOf("岁");
        string age = note.Substring(ageStart, ageEnd - ageStart);
        Console.WriteLine($"年龄: {age}"); // 输出: 62

        // 8. 字符串比较
        bool isEqual = note.Equals("其他记录", StringComparison.OrdinalIgnoreCase); // 忽略大小写比较
        bool startsWith = note.StartsWith("患者");  // 检查开头
        bool endsWith = note.EndsWith("mmHg");    // 检查结尾

        // 9. 字符串拼接
        string[] vitals = { "体温: 37.2℃", "血压: 120/80mmHg", "心率: 75次/分" };
        string summary = string.Join(", ", vitals);
        Console.WriteLine($"生命体征: {summary}");

        // 10. 字符串构建（处理大量字符串拼接）
        StringBuilder noteBuilder = new StringBuilder();
        noteBuilder.AppendLine("患者基本信息：");
        noteBuilder.AppendLine($"姓名：张三");
        noteBuilder.AppendLine($"年龄：{age}岁");
        noteBuilder.AppendLine($"生命体征：{summary}");
        string finalNote = noteBuilder.ToString();
    }
}
```

## 4. 继承

继承是面向对象编程的核心概念之一，在医院管理系统中有很多应用场景。

### 4.1 继承的基本概念

1. 继承的本质：

   - 代码重用
   - 建立类之间的父子关系
   - 实现多态性

2. 继承的特性：
   - 单根性：一个类只能有一个直接父类
   - 传递性：子类继承父类的所有特性，包括父类从其父类继承的特性
   - 子类可以扩展父类的功能
   - 子类可以重写父类的方法

### 4.2 医院员工继承体系示例

```csharp
// 基类：医院员工
public abstract class HospitalEmployee
{
    protected string id;
    protected string name;
    public string Department { get; set; }
    public DateTime HireDate { get; set; }

    // 构造函数
    public HospitalEmployee(string id, string name)
    {
        this.id = id;
        this.name = name;
    }

    // 虚方法 - 允许子类重写
    public virtual string GetDutyDescription()
    {
        return $"{name}在{Department}工作";
    }

    // 抽象方法 - 子类必须实现
    public abstract decimal CalculateSalary();
}

// 护士类
public class Nurse : HospitalEmployee
{
    public string NursingLevel { get; set; }
    public string Shift { get; set; }
    private decimal baseSalary;

    public Nurse(string id, string name, string level)
        : base(id, name)
    {
        NursingLevel = level;
        SetBaseSalary();
    }

    private void SetBaseSalary()
    {
        switch (NursingLevel)
        {
            case "初级": baseSalary = 5000M; break;
            case "中级": baseSalary = 7000M; break;
            case "高级": baseSalary = 9000M; break;
            default: baseSalary = 4000M; break;
        }
    }

    public override string GetDutyDescription()
    {
        return $"{name}是{Department}的{NursingLevel}级护士，{Shift}班次";
    }

    public override decimal CalculateSalary()
    {
        decimal shiftBonus = Shift == "夜班" ? 1000M : 0M;
        return baseSalary + shiftBonus;
    }
}

// 主管护士类
public class HeadNurse : Nurse
{
    public List<Nurse> TeamMembers { get; set; }
    private const decimal MANAGEMENT_BONUS = 2000M;

    public HeadNurse(string id, string name)
        : base(id, name, "主管")
    {
        TeamMembers = new List<Nurse>();
    }

    public void AddTeamMember(Nurse nurse)
    {
        TeamMembers.Add(nurse);
    }

    public override decimal CalculateSalary()
    {
        return base.CalculateSalary() + MANAGEMENT_BONUS;
    }

    // 新增的管理方法
    public string GenerateTeamReport()
    {
        StringBuilder report = new StringBuilder();
        report.AppendLine($"{Department}护理团队报告");
        report.AppendLine($"主管护士：{name}");
        report.AppendLine($"团队成员：{TeamMembers.Count}人");
        foreach (var nurse in TeamMembers)
        {
            report.AppendLine($"- {nurse.GetDutyDescription()}");
        }
        return report.ToString();
    }
}
```

## 5. 集合

在医院信息系统中，我们经常需要处理多个数据项的集合，比如病人列表、药品库存等。C#提供了多种集合类型供我们使用。

### 5.1 ArrayList 详解

ArrayList 是一个非泛型集合，可以存储任意类型的对象。

特点：

1. 动态大小：自动扩展容量
2. 可以存储不同类型的数据
3. 需要类型转换，性能较差
4. 不建议在新代码中使用，推荐使用泛型 List<T>

```csharp
public class PatientListManager
{
    private ArrayList patientList = new ArrayList();

    public void DemonstrateArrayList()
    {
        // 添加不同类型的数据
        patientList.Add("张三");           // 字符串
        patientList.Add(new Patient());    // 患者对象
        patientList.Add(42);               // 数字

        // 容量和数量
        Console.WriteLine($"容量: {patientList.Capacity}"); // 默认4，按需翻倍增长
        Console.WriteLine($"实际数量: {patientList.Count}");

        // 插入元素
        patientList.Insert(0, "急诊病人");

        // 检查包含
        bool hasPatient = patientList.Contains("张三");

        // 遍历（需要类型转换）
        foreach (object item in patientList)
        {
            if (item is Patient patient)
            {
                Console.WriteLine($"病人: {patient.Name}");
            }
        }

        // 删除操作
        patientList.Remove("张三");        // 删除特定元素
        patientList.RemoveAt(0);          // 删除指定位置的元素
        patientList.Clear();              // 清空集合
    }
}
```

### 5.2 Hashtable 与 Dictionary 对比

键值对集合在医疗系统中很常用，比如药品库存管理。

#### Hashtable（非泛型）：

- 键和值都是 object 类型
- 需要类型转换
- 性能较差
- 不建议在新代码中使用

#### Dictionary<TKey, TValue>（泛型）：

- 类型安全
- 性能更好
- 推荐使用

```csharp
public class MedicineInventoryManager
{
    // Hashtable示例（旧方式）
    private Hashtable medicineStockOld = new Hashtable();

    // Dictionary示例（推荐方式）
    private Dictionary<string, int> medicineStock = new Dictionary<string, int>();

    public void CompareCollections()
    {
        // Hashtable操作
        medicineStockOld.Add("阿莫西林", 100);
        medicineStockOld["布洛芬"] = 50;

        // 类型转换问题示例
        int oldStock = (int)medicineStockOld["阿莫西林"]; // 需要显式转换

        // Dictionary操作
        medicineStock.Add("阿莫西林", 100);
        medicineStock["布洛芬"] = 50;

        // 安全的值获取
        if (medicineStock.TryGetValue("阿莫西林", out int stock))
        {
            Console.WriteLine($"阿莫西林库存: {stock}");
        }

        // 检查键是否存在
        if (medicineStock.ContainsKey("布洛芬"))
        {
            medicineStock["布洛芬"] -= 10; // 减少库存
        }

        // 遍历比较
        foreach (DictionaryEntry entry in medicineStockOld)
        {
            Console.WriteLine($"药品: {entry.Key}, 库存: {entry.Value}");
        }

        foreach (KeyValuePair<string, int> kvp in medicineStock)
        {
            Console.WriteLine($"药品: {kvp.Key}, 库存: {kvp.Value}");
        }

        // 仅遍历键或值
        foreach (string medicine in medicineStock.Keys)
        {
            Console.WriteLine($"药品: {medicine}");
        }

        // 转换为列表
        List<string> medicineList = medicineStock.Keys.ToList();
    }
}
```

### 5.3 List<T>详解

List<T>是最常用的泛型集合类型，提供类型安全和更好的性能。

```csharp
public class NursingScheduleManager
{
    private List<Nurse> nurses = new List<Nurse>();

    public void DemonstrateList()
    {
        // 添加元素
        nurses.Add(new Nurse("N001", "张护士", "初级"));
        nurses.Add(new Nurse("N002", "李护士", "中级"));

        // 批量添加
        var newNurses = new List<Nurse>
        {
            new Nurse("N003", "王护士", "高级"),
            new Nurse("N004", "赵护士", "中级")
        };
        nurses.AddRange(newNurses);

        // 查找操作
        Nurse foundNurse = nurses.Find(n => n.NursingLevel == "中级");
        List<Nurse> juniorNurses = nurses.FindAll(n => n.NursingLevel == "初级");

        // 排序
        nurses.Sort((n1, n2) => n1.NursingLevel.CompareTo(n2.NursingLevel));

        // LINQ操作
        var dayShiftNurses = nurses.Where(n => n.Shift == "白班").ToList();
        var nurseCount = nurses.Count(n => n.NursingLevel == "中级");
        var orderedNurses = nurses.OrderBy(n => n.Name).ToList();

        // 转换
        var nurseNames = nurses.Select(n => n.Name).ToList();

        // 检查条件
        bool hasNightNurse = nurses.Any(n => n.Shift == "夜班");
        bool allJunior = nurses.All(n => n.NursingLevel == "初级");

        // 删除操作
        nurses.Remove(foundNurse);
        nurses.RemoveAll(n => n.NursingLevel == "实习");

        // 范围操作
        var someNurses = nurses.GetRange(0, 2); // 获取前两个护士
        nurses.RemoveRange(0, 2);              // 删除前两个护士
    }
}
```

## 6. 类型转换详解

在医院信息系统中，类型转换经常用于处理不同类型的医护人员和病历记录。

### 6.1 类型转换的基本概念

1. 隐式转换：自动进行，安全无数据丢失
2. 显式转换：需要手动进行，可能存在数据丢失风险
3. 引用类型转换：涉及继承关系的类型转换

### 6.2 医疗系统中的类型转换示例

```csharp
public class StaffManager
{
    public void DemonstrateTypeConversion()
    {
        // 基本类型转换
        int heartRate = 75;
        double hrDouble = heartRate;    // 隐式转换
        decimal hrDecimal = (decimal)hrDouble; // 显式转换

        // 字符串转换
        string hrString = heartRate.ToString();
        int parsedHR = int.Parse("75"); // 字符串转数字

        // TryParse安全转换
        if (int.TryParse("75", out int result))
        {
            Console.WriteLine($"转换成功：{result}");
        }

        // 引用类型转换示例
        HospitalEmployee employee = new Nurse("N001", "张护士", "初级");

        // 1. is运算符 - 检查类型
        if (employee is Nurse)
        {
            Console.WriteLine("这是一名护士");
        }
        else if (employee is Doctor)
        {
            Console.WriteLine("这是一名医生");
        }

        // 2. as运算符 - 安全转换
        Nurse nurse = employee as Nurse;
        if (nurse != null)
        {
            Console.WriteLine($"护士级别: {nurse.NursingLevel}");
        }

        // 3. 显式转换
        try
        {
            Nurse nurse2 = (Nurse)employee;
            Console.WriteLine($"转换成功: {nurse2.NursingLevel}");
        }
        catch (InvalidCastException ex)
        {
            Console.WriteLine($"转换失败: {ex.Message}");
        }

        // 4. 模式匹配（C# 7.0+）
        if (employee is Nurse nurseMatch)
        {
            Console.WriteLine($"匹配成功: {nurseMatch.NursingLevel}");
        }

        // 5. switch模式匹配
        string GetEmployeeInfo(HospitalEmployee emp)
        {
            return emp switch
            {
                Nurse n => $"护士 {n.Name}, 级别 {n.NursingLevel}",
                Doctor d => $"医生 {d.Name}, 专业 {d.Specialty}",
                _ => $"员工 {emp.Name}"
            };
        }
    }

    // 自定义转换示例
    public class VitalSignsConverter
    {
        public static implicit operator string(VitalSigns vs)
        {
            return $"体温: {vs.Temperature}℃, 心率: {vs.HeartRate}次/分";
        }

        public static explicit operator VitalSigns(string data)
        {
            // 简单示例，实际应该有更复杂的解析逻辑
            var parts = data.Split(',');
            return new VitalSigns
            {
                Temperature = decimal.Parse(parts[0]),
                HeartRate = int.Parse(parts[1])
            };
        }
    }
}
```

### 6.3 类型转换最佳实践

1. 优先使用安全的转换方法：

   - 使用 TryParse 而不是 Parse
   - 使用 as 运算符而不是直接类型转换
   - 总是检查转换结果

2. 处理转换异常：
   ```csharp
   public decimal ParseTemperature(string temp)
   {
       try
       {
           return decimal.Parse(temp);
       }
       catch (FormatException)
       {
           Console.WriteLine("温度格式不正确");
           return 0;
       }
       catch (OverflowException)
       {
           Console.WriteLine("温度值超出范围");
           return 0;
       }
   }
   ```

## 总结

在这篇文章中，我们通过医院信息系统的实际场景，详细探讨了 C#中的几个重要概念：

1. **命名空间**

   - 理解了命名空间的组织结构
   - 掌握了多种使用命名空间的方法
   - 学会了项目引用和命名空间导入

2. **值类型和引用类型**

   - 理解了两种类型的本质区别
   - 掌握了内存存储的不同方式
   - 学会了通过医疗数据来理解类型特性

3. **字符串处理**

   - 掌握了常用的字符串操作方法
   - 学会了处理医疗记录文本
   - 理解了字符串的特殊性质

4. **继承**

   - 通过医院员工体系理解继承概念
   - 掌握了继承的特性和使用方法
   - 学会了方法重写和构造函数调用

5. **集合**

   - 理解了不同集合类型的特点
   - 掌握了集合的常用操作
   - 学会了选择合适的集合类型

6. **类型转换**
   - 掌握了各种类型转换方法
   - 学会了安全的类型转换实践
   - 理解了类型转换在医疗系统中的应用

这些概念将帮助我们构建更复杂的医疗信息系统功能。
