---
title: EF Core 6 新功能汇总（一）
slug: entity-framework-core-6-features-part-1
description: 在这篇文章中，你将看到 EF Core 6 中的十个新功能，包括新的特性标注，对时态表、稀疏列的支持，以及其他新功能。
date: 2022-06-02 21:41:47
copyright: Reprinted
author: liamwang 精致码农
originalTitle: EF Core 6 新功能汇总（一）
originalLink: https://mp.weixin.qq.com/s/VpqEWQPdEJUw_HHNeqBPdg
draft: False
cover: https://img1.dotnet9.com/2022/06/cover_04.jpg
categories: .NET
tags: 
    - .NET
    - ORM
    - EF Core
---

在这篇文章中，你将看到 EF Core 6 中的十个新功能，包括新的特性标注，对时态表、稀疏列的支持，以及其他新功能。

## 1 Unicode 特性

在 EF Core 6.0 中，新的 `UnicodeAttribute` 允许你将一个字符串属性映射到一个非 `Unicode` 列，而不需要直接指定数据库类型。当数据库系统只支持 `Unicode` 类型时，`Unicode` 特性会被忽略。

```csharp
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }

    [Unicode(false)]
    [MaxLength(22)]
    public string Isbn { get; set; }
}
```

对应的迁移代码：

```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.CreateTable(
        name: "Books",
        columns: table => new
        {
            Id = table.Column<int>(type: "int", nullable: false)
                        .Annotation("SqlServer:Identity", "1, 1"),
            Title = table.Column<string>(type: "nvarchar(max)", nullable: true),
            Isbn = table.Column<string>(type: "varchar(22)", unicode: false, maxLength: 22, nullable: true)
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_Books", x => x.Id);
        });
}
```

## 2 Precision 特性

在 EF Core 6.0 之前，你可以用 Fluent API 配置精度。现在，你也可以用数据标注和一个新的 `PrecisionAttribute` 来做这件事。

```csharp
public class Product
{
    public int Id { get; set; }

    [Precision(precision: 10, scale: 2)]
    public decimal Price { get; set; }
}
```

对应的迁移代码：

```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.CreateTable(
        name: "Products",
        columns: table => new
        {
            Id = table.Column<int>(type: "int", nullable: false)
                .Annotation("SqlServer:Identity", "1, 1"),
            Price = table.Column<decimal>(type: "decimal(10,2)", precision: 10, scale: 2, nullable: false)
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_Products", x => x.Id);
        });
}
```

## 3 EntityTypeConfiguration 特性

从 EF Core 6.0 开始，你可以在实体类型上放置一个新的 `EntityTypeConfiguration` 特性，这样 EF Core 就可以找到并使用适当的配置。在此之前，类的配置必须被实例化并从 `OnModelCreating` 方法中调用。

```csharp
public class ProductConfiguration : IEntityTypeConfiguration<Product>
{
    public void Configure(EntityTypeBuilder<Product> builder)
    {
        builder.Property(p => p.Name).HasMaxLength(250);
        builder.Property(p => p.Price).HasPrecision(10, 2);
    }
}
[EntityTypeConfiguration(typeof(ProductConfiguration))]
public class Product
{
    public int Id { get; set; }
    public decimal Price { get; set; }
    public string Name { get; set; }
}
```

## 4 Column 特性

当你在模型中使用继承时，你可能不满意创建的表中默认的 EF Core 列顺序。在 EF Core 6.0 中，你可以用 `ColumnAttribute` 指定列的顺序。

此外，你还可以使用新的 Fluent API--`HasColumnOrder()` 来实现。

```csharp
public class EntityBase
{
    [Column(Order = 1)]
    public int Id { get; set; }
    [Column(Order = 99)]
    public DateTime UpdatedOn { get; set; }
    [Column(Order = 98)]
    public DateTime CreatedOn { get; set; }
}
public class Person : EntityBase
{
    [Column(Order = 2)]
    public string FirstName { get; set; }
    [Column(Order = 3)]
    public string LastName { get; set; }
    public ContactInfo ContactInfo { get; set; }
}
public class Employee : Person
{
    [Column(Order = 4)]
    public string Position { get; set; }
    [Column(Order = 5)]
    public string Department { get; set; }
}
[Owned]
public class ContactInfo
{
    [Column(Order = 10)]
    public string Email { get; set; }
    [Column(Order = 11)]
    public string Phone { get; set; }
}
```

对应的迁移代码：

```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.CreateTable(
        name: "Employees",
        columns: table => new
        {
            Id = table.Column<int>(type: "int", nullable: false)
                .Annotation("SqlServer:Identity", "1, 1"),
            FirstName = table.Column<string>(type: "nvarchar(max)", nullable: true),
            LastName = table.Column<string>(type: "nvarchar(max)", nullable: true),
            Position = table.Column<string>(type: "nvarchar(max)", nullable: true),
            Department = table.Column<string>(type: "nvarchar(max)", nullable: true),
            ContactInfo_Email = table.Column<string>(type: "nvarchar(max)", nullable: true),
            ContactInfo_Phone = table.Column<string>(type: "nvarchar(max)", nullable: true),
            CreatedOn = table.Column<DateTime>(type: "datetime2", nullable: false),
            UpdatedOn = table.Column<DateTime>(type: "datetime2", nullable: false)
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_Employees", x => x.Id);
        });
}
```

## 5 时态表

EF Core 6.0 支持 SQL Server 的时态表。一个表可以被配置成一个具有 SQL Server 默认的时间戳和历史表的时态表。

```csharp
public class ExampleContext : DbContext
{
    public DbSet<Person> People { get; set; }
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<Person>()
            .ToTable("People", b => b.IsTemporal());
    }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
            => options.UseSqlServer("Server=(localdb)\\mssqllocaldb;Database=TemporalTables;Trusted_Connection=True;");
}
public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}
```

对应的迁移代码：

```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.CreateTable(
        name: "People",
        columns: table => new
        {
            Id = table.Column<int>(type: "int", nullable: false)
                .Annotation("SqlServer:Identity", "1, 1"),
            Name = table.Column<string>(type: "nvarchar(max)", nullable: true),
            PeriodEnd = table.Column<DateTime>(type: "datetime2", nullable: false)
                .Annotation("SqlServer:IsTemporal", true)
                .Annotation("SqlServer:TemporalPeriodEndColumnName", "PeriodEnd")
                .Annotation("SqlServer:TemporalPeriodStartColumnName", "PeriodStart"),
            PeriodStart = table.Column<DateTime>(type: "datetime2", nullable: false)
                .Annotation("SqlServer:IsTemporal", true)
                .Annotation("SqlServer:TemporalPeriodEndColumnName", "PeriodEnd")
                .Annotation("SqlServer:TemporalPeriodStartColumnName", "PeriodStart")
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_People", x => x.Id);
        })
        .Annotation("SqlServer:IsTemporal", true)
        .Annotation("SqlServer:TemporalHistoryTableName", "PersonHistory")
        .Annotation("SqlServer:TemporalHistoryTableSchema", null)
        .Annotation("SqlServer:TemporalPeriodEndColumnName", "PeriodEnd")
        .Annotation("SqlServer:TemporalPeriodStartColumnName", "PeriodStart");
}
```

你可以用以下方法查询和检索历史数据：

- TemporalAsOf
- TemporalAll
- TemporalFromTo
- TemporalBetween
- TemporalContainedIn

使用时态表：

```csharp
using ExampleContext context = new();
context.People.Add(new() { Name = "Oleg" });
context.People.Add(new() { Name = "Steve" });
context.People.Add(new() { Name = "John" });
await context.SaveChangesAsync();

var people = await context.People.ToListAsync();
foreach (var person in people)
{
    var personEntry = context.Entry(person);
    var validFrom = personEntry.Property<DateTime>("PeriodStart").CurrentValue;
    var validTo = personEntry.Property<DateTime>("PeriodEnd").CurrentValue;
    Console.WriteLine($"Person {person.Name} valid from {validFrom} to {validTo}");
}
// Output:
// Person Oleg valid from 06-Nov-21 17:50:39 PM to 31-Dec-99 23:59:59 PM
// Person Steve valid from 06-Nov-21 17:50:39 PM to 31-Dec-99 23:59:59 PM
// Person John valid from 06-Nov-21 17:50:39 PM to 31-Dec-99 23:59:59 PM
```

查询历史数据：

```csharp
var oleg = await context.People.FirstAsync(x => x.Name == "Oleg");
context.People.Remove(oleg);
await context.SaveChangesAsync();
var history = context
    .People
    .TemporalAll()
    .Where(e => e.Name == "Oleg")
    .OrderBy(e => EF.Property<DateTime>(e, "PeriodStart"))
    .Select(
        p => new
        {
            Person = p,
            PeriodStart = EF.Property<DateTime>(p, "PeriodStart"),
            PeriodEnd = EF.Property<DateTime>(p, "PeriodEnd")
        })
    .ToList();
foreach (var pointInTime in history)
{
    Console.WriteLine(
        $"Person {pointInTime.Person.Name} existed from {pointInTime.PeriodStart} to {pointInTime.PeriodEnd}");
}

// Output:
// Person Oleg existed from 06-Nov-21 17:50:39 PM to 06-Nov-21 18:11:29 PM
```

检索历史数据：

```csharp
var removedOleg = await context
    .People
    .TemporalAsOf(history.First().PeriodStart)
    .SingleAsync(e => e.Name == "Oleg");

Console.WriteLine($"Id = {removedOleg.Id}; Name = {removedOleg.Name}");
// Output:
// Id = 1; Name = Oleg
```

了解更多关于时态表的信息：[https://devblogs.microsoft.com/dotnet/prime-your-flux-capacitor-sql-server-temporal-tables-in-ef-core-6-0/](https://devblogs.microsoft.com/dotnet/prime-your-flux-capacitor-sql-server-temporal-tables-in-ef-core-6-0/)

## 6 稀疏列

EF Core 6.0 支持 SQL Server 稀疏列。在使用 TPH（table per hierarchy）继承映射时，它可能很有用。

```csharp
public class ExampleContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<Employee> Employees { get; set; }
    public DbSet<User> Users { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<User>()
            .Property(e => e.Login)
            .IsSparse();

        modelBuilder
            .Entity<Employee>()
            .Property(e => e.Position)
            .IsSparse();
    }

    protected override void OnConfiguring(DbContextOptionsBuilder options)
            => options.UseSqlServer("Server=(localdb)\\mssqllocaldb;Database=SparseColumns;Trusted_Connection=True;");
}

public class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}
public class User : Person
{
    public string Login { get; set; }
}
public class Employee : Person
{
    public string Position { get; set; }
}
```

对应迁移代码：

```csharp
protected override void Up(MigrationBuilder migrationBuilder)
{
    migrationBuilder.CreateTable(
        name: "People",
        columns: table => new
        {
            Id = table.Column<int>(type: "int", nullable: false)
                .Annotation("SqlServer:Identity", "1, 1"),
            Name = table.Column<string>(type: "nvarchar(max)", nullable: false),
            Discriminator = table.Column<string>(type: "nvarchar(max)", nullable: false),
            Position = table.Column<string>(type: "nvarchar(max)", nullable: true)
                .Annotation("SqlServer:Sparse", true),
            Login = table.Column<string>(type: "nvarchar(max)", nullable: true)
                .Annotation("SqlServer:Sparse", true)
        },
        constraints: table =>
        {
            table.PrimaryKey("PK_People", x => x.Id);
        });
}
```

稀疏列有限制，具体请看文档：[https://docs.microsoft.com/en-us/sql/relational-databases/tables/use-sparse-columns?view=sql-server-ver15](https://docs.microsoft.com/en-us/sql/relational-databases/tables/use-sparse-columns?view=sql-server-ver15)

## 7 EF Core 中的最小 API

EF Core 6.0 有它自己的最小 API。新的扩展方法可在同一行代码注册一个 DbContext 类型，并提供一个数据库 Provider 的配置。

```csharp
const string AccountKey = "[CosmosKey]";

var builder = WebApplication.CreateBuilder(args);
builder.Services.AddSqlServer<MyDbContext>(@"Server = (localdb)\mssqllocaldb; Database = MyDatabase");

// OR
builder.Services.AddSqlite<MyDbContext>("Data Source=mydatabase.db");

// OR
builder.Services.AddCosmos<MyDbContext>($"AccountEndpoint=https://localhost:8081/;AccountKey={AccountKey}", "MyDatabase");

var app = builder.Build();
app.Run();

class MyDbContext : DbContext
{ }
```

## 8 迁移包

在 EF Core 6.0 中，有一个新的有利于 DevOps 的功能--迁移包。它允许创建一个包含迁移的小型可执行程序。你可以在 CD 中使用它。不需要复制源代码或安装 .NET SDK（只有运行时）。

CLI：

```shell
dotnet ef migrations bundle --project MigrationBundles
```

Package Manager Console:

```shell
Bundle-Migration
```

![](https://img1.dotnet9.com/2022/06/0401.jpg)

更多介绍：[https://devblogs.microsoft.com/dotnet/introducing-devops-friendly-ef-core-migration-bundles/](https://devblogs.microsoft.com/dotnet/introducing-devops-friendly-ef-core-migration-bundles/)

## 9 预设模型配置

EF Core 6.0 引入了一个预设模型配置。它允许你为一个给定的类型指定一次映射配置。例如，在处理值对象时，它可能很有帮助。

```csharp
public class ExampleContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<Product> Products { get; set; }

    protected override void ConfigureConventions(ModelConfigurationBuilder configurationBuilder)
    {
        configurationBuilder
            .Properties<string>()
            .HaveMaxLength(500);

        configurationBuilder
            .Properties<DateTime>()
            .HaveConversion<long>();

        configurationBuilder
            .Properties<decimal>()
            .HavePrecision(12, 2);

        configurationBuilder
            .Properties<Address>()
            .HaveConversion<AddressConverter>();
    }
}
public class Product
{
    public int Id { get; set; }
    public decimal Price { get; set; }
}
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public DateTime BirthDate { get; set; }
    public Address Address { get; set; }
}
public class Address
{
    public string Country { get; set; }
    public string Street { get; set; }
    public string ZipCode { get; set; }
}
public class AddressConverter : ValueConverter<Address, string>
{
    public AddressConverter()
        : base(
            v => JsonSerializer.Serialize(v, (JsonSerializerOptions)null),
            v => JsonSerializer.Deserialize<Address>(v, (JsonSerializerOptions)null))
    {
    }
}
```

## 10 已编译模型

在 EF Core 6.0 中，你可以生成已编译的模型（compiled models）。当你有一个大的模型，而你的 EF Core 启动很慢时，这个功能是有意义的。你可以使用 CLI 或包管理器控制台来做。

```csharp
public class ExampleContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder options)
    {
        options.UseModel(CompiledModelsExample.ExampleContextModel.Instance)
        options.UseSqlServer("Server=(localdb)\\mssqllocaldb;Database=SparseColumns;Trusted_Connection=True;");
    }
}
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
```

CLI：

```shell
dotnet ef dbcontext optimize -c ExampleContext -o CompliledModels -n CompiledModelsExample
```

Package Manager Console：

```shell
Optimize-DbContext -Context ExampleContext -OutputDir CompiledModels -Namespace CompiledModelsExample
```

更多关于已编译模型及其限制的介绍：

```shell
https://devblogs.microsoft.com/dotnet/announcing-entity-framework-core-6-0-preview-5-compiled-models/
https://docs.microsoft.com/en-us/ef/core/what-is-new/ef-core-6.0/whatsnew#limitations
```

## 11 结尾

你可以在我的 GitHub 找到本文所有示例代码：[https://github.com/okyrylchuk/dotnet6_features/tree/main/EF%20Core%206#miscellaneous-enhancements](https://github.com/okyrylchuk/dotnet6_features/tree/main/EF%20Core%206#miscellaneous-enhancements)

> 原文：https://blog.okyrylchuk.dev/entity-framework-core-6-features-part-1
>
> 作者：Oleg Kyrylchuk
>
> 翻译：精致码农
