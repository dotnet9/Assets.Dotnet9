---
title: EF Core 6 新功能汇总（四）
slug: entity-framework-core-6-features-part-4
description: 在这篇文章中，你将看到 EF Core 对 SQLite、In-memory 提供者和 EF.Functions.Contains 方法的改进。
date: 2022-06-02 22:12:35
copyright: Reprinted
author: liamwang 精致码农
originalTitle: EF Core 6 新功能汇总（四）
originalLink: https://mp.weixin.qq.com/s/BU8FKXS95WlvblVPBulC5g
draft: False
cover: https://img1.dotnet9.com/2022/06/cover_04.jpg
categories: 
    - EF Core
tags: 
    - .NET
    - ORM
    - EF Core
---

在这篇文章中，你将看到 EF Core 对 SQLite、In-memory 提供者和 EF.Functions.Contains 方法的改进。

这是 EF Core 6 新功能汇总的第四篇文章：

- [EF Core 6 新功能汇总（一）](https://mp.weixin.qq.com/s/VpqEWQPdEJUw_HHNeqBPdg)
- [EF Core 6 新功能汇总（二）](https://mp.weixin.qq.com/s/IJd0pwvQhCIohGR0dfekew)
- [EF Core 6 新功能汇总（三）](https://mp.weixin.qq.com/s/ZRNu1Lvx6ZYr0kStMjc_Ug)
- [EF Core 6 新功能汇总（四）](https://mp.weixin.qq.com/s/BU8FKXS95WlvblVPBulC5g)

## 1 SQLite 支持 DateOnly 和 TimeOnly

在 EF Core 6.0 中，SQLite 提供者支持新的 `DateOnly` 和 `TimeOnly` 类型。它将它们存储为 `TEXT`。

```csharp
using var context = new ExampleContext();

var query1 = context.People
    .Where(p => p.Birthday < new DateOnly(2000, 1, 1))
    .ToQueryString();

Console.WriteLine(query1);
// SELECT "p"."Id", "p"."Birthday", "p"."Name"
// FROM "People" AS "p"
// WHERE "p"."Birthday" < '2000-01-01'

var query2 = context.Notifications
    .Where(n => n.AllowedFrom >= new TimeOnly(8, 0) && n.AllowedTo <= new TimeOnly(16, 0))
    .ToQueryString();

Console.WriteLine(query2);
// SELECT "n"."Id", "n"."AllowedFrom", "n"."AllowedTo"
// FROM "Notifications" AS "n"
// WHERE("n"."AllowedFrom" >= '08:00:00') AND("n"."AllowedTo" <= '16:00:00')

class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateOnly Birthday { get; set; }
}
class Notification
{
    public int Id { get; set; }
    public TimeOnly AllowedFrom { get; set; }
    public TimeOnly AllowedTo { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Person> People { get; set; }
    public DbSet<Notification> Notifications { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite(@"Data Source=Db\DateOnlyTimeOnly.db");
}
```

## 2 SQLite 连接是池化的

SQLite 数据库是一个文件。因此，在大多数情况下，创建一个连接是很快的。然而，打开一个加密数据库的连接会非常慢。因此，在 EF Core 6 中，SQLite 连接现在是池化的，就像其他数据库提供者一样。

```csharp
class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=EncryptedDb.db;Mode=ReadWriteCreate;Password=password");
}
```

## 3 SQLite 中的命令超时

在 EF Core 6 的 SQLite 中，支持添加 `Command Timeout` 命令到连接字符串中，你可以用它来指定 SQLite 的默认超时时间。

```csharp
class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Person> People { get; set; }

    // 60 seconds as the default timeout for commands created by connection
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlite("Data Source=Test.db;Command Timeout=60");
}
```

## 4 SQLite 中的保存点

在 EF Core 6.0 中，SQLite 支持保存点(Savepoints)。你可以保存、回滚和释放保存点。

```csharp
var dbPath = Path.GetFullPath(Path.Combine(AppContext.BaseDirectory, "..\\..\\..\\Savepoints.db"));

using var connection = new SqliteConnection($"Data Source={dbPath}");
connection.Open();
using var transaction = connection.BeginTransaction();

// The insert is committed to the database
using (var command = connection.CreateCommand())
{
    command.CommandText = @"INSERT INTO People (Name) VALUES ('Oleg')";
    command.ExecuteNonQuery();
}

transaction.Save("MySavepoint");

// The update is not commited since savepoint is rolled back before commiting the transaction
using (var command = connection.CreateCommand())
{
    command.CommandText = @"UPDATE People SET Name = 'Not Oleg' WHERE Id = 1";
    command.ExecuteNonQuery();
}

transaction.Rollback("MySavepoint");
transaction.Commit();
```

## 5 内存数据库验证必需属性

在 EF Core 6.0 中，内存(In-memory)数据库验证了必需（In-momory）的属性。如果你试图保存一个实体的必需属性值为空，就会出现异常。如果有必要，你可以禁用这个验证。

```csharp
using var context = new ExampleContext();

var blog = new Blog();
context.Blogs.Add(blog);

await context.SaveChangesAsync();
// Unhandled exception. Microsoft.EntityFrameworkCore.DbUpdateException:
// Required properties '{'Title'}' are missing for the instance of entity
// type 'Blog' with the key value '{Id: 1}'.

class Blog
{
    public int Id { get; set; }
    [Required]
    public string Title { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options
        .EnableSensitiveDataLogging()
        .LogTo(Console.WriteLine, new[] { InMemoryEventId.ChangesSaved })
        .UseInMemoryDatabase("ValidateRequiredProps");

    //  To disable the validation
    //  .UseInMemoryDatabase("ValidateRequiredProps", b => b.EnableNullChecks(false));
}
```

## 6 EF.Functions.Contains 方法

在 EF Core 6.0 中，你可以使用 `EF.Functions.Contains` 方法来处理使用值转换器映射的列（也可以处理二进制列）。

```csharp
using var context = new ExampleContext();

var query = context.People
    .Where(e => EF.Functions.Contains(e.FullName, "Oleg"))
    .ToQueryString();

Console.WriteLine(query);
// SELECT[p].[Id], [p].[FullName]
// FROM[People] AS[p]
// WHERE CONTAINS([p].[FullName], N'Oleg')

class Person
{
    public int Id { get; set; }
    public FullName FullName { get; set; }
}
public class FullName
{
    public string FirstName { get; set; }
    public string LastName { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Person> People { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Person>()
            .Property(x => x.FullName)
            .HasConversion(
                v => JsonSerializer.Serialize(v, (JsonSerializerOptions)null),
                v => JsonSerializer.Deserialize<FullName>(v, (JsonSerializerOptions)null));
    }

    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6Contains");
}
```

## 7 结尾

本文所有代码示例都可以在我的 GitHub 中找到：[https://github.com/okyrylchuk/dotnet6_features/tree/main/EF%20Core%206#miscellaneous-enhancements](https://github.com/okyrylchuk/dotnet6_features/tree/main/EF%20Core%206#miscellaneous-enhancements)

> 原文：https://blog.okyrylchuk.dev/entity-framework-core-6-features-part-3
>
> 作者：Oleg Kyrylchuk
>
> 翻译：精致码农
