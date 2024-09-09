---
title: EF Core 6 新功能汇总（三）
slug: entity-framework-core-6-features-part-3
description: 在这篇文章中，我将重点介绍 EF Core 6 中 LINQ 查询功能的增强。
date: 2022-06-02 22:02:35
copyright: Reprinted
author: liamwang 精致码农
originalTitle: EF Core 6 新功能汇总（三）
originalLink: https://mp.weixin.qq.com/s/ZRNu1Lvx6ZYr0kStMjc_Ug
draft: False
cover: https://img1.dotnet9.com/2022/06/cover_04.jpg
categories: 
    - .NET
tags: 
    - .NET
    - ORM
    - EF Core
---

在这篇文章中，我将重点介绍 EF Core 6 中 LINQ 查询功能的增强。

这是 EF Core 6 新功能汇总的第三篇文章：

- [EF Core 6 新功能汇总（一）](https://mp.weixin.qq.com/s/VpqEWQPdEJUw_HHNeqBPdg)
- [EF Core 6 新功能汇总（二）](https://mp.weixin.qq.com/s/IJd0pwvQhCIohGR0dfekew)
- [EF Core 6 新功能汇总（三）](https://mp.weixin.qq.com/s/ZRNu1Lvx6ZYr0kStMjc_Ug)

- 1 对 GroupBy 查询的更好支持

EF Core 6.0 对 `GroupBy` 查询有更好的支持。

- 翻译 `GroupBy` 后面的 `FirstOrDefault`
- 在 `GroupBy` 之后使用 `ThenBy`
- 支持从一个组中选择前 N 个结果

```csharp
using var context = new ExampleContext();
var query = context.People
    .GroupBy(p => p.FirstName)
    .Select(g => g.OrderBy(e => e.FirstName)
        .ThenBy(e => e.LastName)
        .FirstOrDefault())
    .ToQueryString();
Console.WriteLine(query);

class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public int LastName { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Person> People { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6GroupBy");
}
```

翻译后的 SQL：

```sql
SELECT[t0].[Id], [t0].[FirstName], [t0].[LastName]
FROM (
SELECT[p].[FirstName]
   FROM [People] AS [p]
   GROUP BY [p].[FirstName]
) AS[t]
LEFT JOIN(
   SELECT[t1].[Id], [t1].[FirstName], [t1].[LastName]
   FROM (
       SELECT[p0].[Id], [p0].[FirstName], [p0].[LastName],
       ROW_NUMBER() OVER(PARTITION BY [p0].[FirstName]
       ORDER BY [p0].[FirstName], [p0].[LastName]) AS[row]
       FROM[People] AS[p0]
   ) AS[t1]
   WHERE[t1].[row] <= 1
) AS[t0] ON[t].[FirstName] = [t0].[FirstName]
```

## 2 三四个参数的 String.Concat 翻译

以前 EF Core 翻译 `string.Concat` 时只有两个参数。EF Core 6.0 支持翻译三个和四个参数的 `string.Concat`。

```csharp
using var context = new ExampleContext();
string fullName = "SamuelLanghorneClemens";
var query = context.Blogs
    .Where(b => string.Concat(b.FirstName, b.MiddleName, b.LastName) == fullName)
    .ToQueryString();
Console.WriteLine(query);

class Blog
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string MiddleName { get; set; }
    public string LastName { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6StringConcat");
}
```

翻译后的 SQL：

```sql
DECLARE @__fullName_0 nvarchar(4000) = N'SamuelLanghorneClemens';

SELECT[b].[Id], [b].[FirstName], [b].[LastName], [b].[MiddleName]
FROM[Blogs] AS[b]
WHERE(COALESCE([b].[FirstName], N'') + (COALESCE([b].[MiddleName], N'') +COALESCE([b].[LastName], N ''))) = @__fullName_0
```

## 3 EF.Functions.FreeText 支持二进制列

以前，尽管 SQL `FreeText` 函数支持二进制列，但你不能在二进制列上使用 `EF.Functions.FreeText` 方法。EF Core 6.0 解决了这个问题。

```csharp
using var context = new ExampleContext();
var query = context.Posts
    .Where(p => EF.Functions.FreeText(EF.Property<string>(p, "Content"), "Searching text"))
    .ToQueryString();
Console.WriteLine(query);

class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public byte[] Content { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .Property(x => x.Content)
            .HasColumnType("varbinary(max)");
    }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6FlexibleTextSearch");
}
```

翻译后的 SQL：

```sql
SELECT "p"."Id", "p"."Name", "p"."PhoneNumber"
FROM "People" AS "p"
WHERE CAST("p"."PhoneNumber" AS TEXT) LIKE '%368%'
```

## 4 EF.Functions.Random

EF Core 6.0 引入了一个新的 `EF.Functions.Random` 方法。它映射了 SQL 函数 `RAND()`。已经实现了对 SQL Server、SQLite 和 Cosmos 的翻译。

```csharp
using var context = new ExampleContext();
var query = context.Posts
    .Where(p => p.Rating == (int)(EF.Functions.Random() * 5.0) + 1)
    .ToQueryString();
Console.WriteLine(query);

class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public int Rating { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6Random");
}
```

翻译后的 SQL：

```sql
SELECT[p].[Id], [p].[Rating], [p].[Title]
FROM[Posts] AS[p]
WHERE[p].[Rating] = (CAST((RAND() * 5.0E0) AS int) + 1)
```

## 5 改进了 SQL Server 的 IsNullOrWhitespace 的翻译

以前，EF Core 将 `string.IsNullOrWhiteSpace` 翻译成在判断前将值进行 `trim` 操作。EF Core 6.0 已经不这么做了。

```csharp
using var context = new ExampleContext();
var query = context.Entities
                    .Where(e => string.IsNullOrWhiteSpace(e.Property))
                    .ToQueryString();
Console.WriteLine(query);

class Entity
{
    public int Id { get; set; }
    public string Property { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Entity> Entities { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6IsNullOrWhiteSpace");
}
```

以前翻译的 SQL：

```sql
SELECT [e].[Id], [e].[Property]
FROM [Entities] AS[e]
WHERE [e].[Property] IS NULL OR (LTRIM(RTRIM([e].[Property])) = N'')
```

现在翻译的 SQL：

```sql
SELECT [e].[Id], [e].[Property]
FROM [Entities] AS[e]
WHERE [e].[Property] IS NULL OR ([e].[Property] = N'')
```

## 6 为内存数据库定义查询

在 EF Core 6.0 中，你可以通过一个新的方法 `ToInMemoryQuery` 来定义一个针对内存数据库的查询。这对于创建内存数据库的视图是最有用的。

```csharp
using var context = new ExampleContext();
var blogEn = new Blog
{
    Title = "All about .NET",
    Language = "English",
    Posts = new List<Post>
        {
            new Post { Title = "Post one", Content = "Some content" },
            new Post { Title = "Post two", Content = "Some content" }
        }
};
var blogPl = new Blog
{
    Title = "Wszystko o .NET",
    Language = "Polish",
    Posts = new List<Post>
        {
            new Post { Title = "Pierwszy post", Content = "Treść" }
        }
};
context.Blogs.Add(blogEn);
context.Blogs.Add(blogPl);
await context.SaveChangesAsync();

var postsByLanguages = context.PostsByLanguages.ToList();
postsByLanguages
    .ForEach(p => Console.WriteLine($"{p.PostCount} posts in {p.Language}"));
// Output:
// 2 posts in English
// 1 posts in Polish

class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Content { get; set; }
}
class Blog
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Language { get; set; }
    public ICollection<Post> Posts { get; set; }
}
class PostsByLanguage
{
    public string Language { get; set; }
    public int PostCount { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<PostsByLanguage> PostsByLanguages { get; set; }
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<PostsByLanguage>()
            .HasNoKey()
            .ToInMemoryQuery(
                () => Blogs
                    .GroupBy(c => c.Language)
                    .Select(
                        g =>
                            new PostsByLanguage
                            {
                                Language = g.Key,
                                PostCount = g.Sum(b => b.Posts.Count)
                            }));
    }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseInMemoryDatabase("ToInMemoryQuery");
}
```

## 7 单参数的 Substring 翻译

以前，EF Core 只翻译有两个参数的 `string.Substring` 重载。EF Core 6.0 支持翻译单个参数的 `string.Substring`。

```csharp
using var context = new ExampleContext();
context.People.Add(new Person { Name = "John" });
context.People.Add(new Person { Name = "Bred" });
context.People.Add(new Person { Name = "Ron" });
await context.SaveChangesAsync();

var result = await context.People
    .Select(a => new { Name = a.Name.Substring(1) })
    .ToListAsync();
result.ForEach(p => Console.WriteLine(p.Name));
// Output:
// ohn
// red
// on

class Person
{
    public int Id { get; set; }
    public string Name { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Person> People { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6Substring");
}
```

翻译后的 SQL：

```sql
SELECT SUBSTRING([p].[Name], 1 + 1, LEN([p].[Name])) AS [Name]
FROM [People] AS [p]
```

## 8 非导航集合的分割查询

EF Core 支持将一个 LINQ 查询拆分成多个 SQL 查询。EF Core 6.0 可以分割一个 LINQ 查询，其中非导航集合属性包含在查询投影中。

```csharp
using var context = new ExampleContext();
var blog = new Blog { Name = ".NET Blog"};
blog.Posts.Add(new Post { Title = "First .NET post" });
blog.Posts.Add(new Post { Title = "Second Java post" });
blog.Posts.Add(new Post { Title = "Third .NET post" });
context.Blogs.Add(blog);
await context.SaveChangesAsync();

var blogsWithDotnetPosts = await context.Blogs
    .Select(b => new
    {
        b,
        Posts = b.Posts.Where(p => p.Title.Contains(".NET")),
    })
    .AsSplitQuery()
    .ToListAsync();

class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public ICollection<Post> Posts { get; set; } = new List<Post>();
}
class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public Blog Blog { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6SplitQueries");
}
```

单个 SQL 查询（不用 AsSplitQuery）：

```sql
SELECT [b].[Id], [b].[Name], [t].[BlogId], [t].[Title]
FROM [Blogs] AS [b]
LEFT JOIN (
     SELECT [p].[Id], [p].[BlogId], [p].[Title]
     FROM [Posts] AS [p]
     WHERE [p].[Title] LIKE N'%.NET%'
) AS [t] ON [b].[Id] = [t].[BlogId]
ORDER BY [b].[Id]
```

多个 SQL 查询（使用了 AsSplitQuery）：

```sql
SELECT [b].[Id], [b].[Name]
FROM [Blogs] AS [b]
ORDER BY [b].[Id]

SELECT [t].[Id], [t].[BlogId], [t].[Title], [b].[Id]
FROM [Blogs] AS [b]
INNER JOIN (
     SELECT [p].[Id], [p].[BlogId], [p].[Title]
     FROM [Posts] AS [p]
     WHERE [p].[Title] LIKE N'%.NET%'
) AS [t] ON [b].[Id] = [t].[BlogId]
ORDER BY [b].[Id]
```

## 9 删除最后的 ORDER BY 子句

当连接相关实体时，EF Core 添加了 ORDER BY 子句，以确保给定实体的所有相关实体被分组。然而，最后一个子句并不是必须的，而且会对性能产生影响。EF Core 6.0 删除了它。

```csharp
using var context = new ExampleContext();
var query = context.Blogs
    .Include(b => b.Posts.Where(p => p.Rating > 3))
    .ToQueryString();
Console.WriteLine(query);

class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public ICollection<Post> Posts { get; set; }
}
class Post
{
    public int Id { get; set; }
    public string Title { get; set; }
    public int Rating { get; set; }
    public Blog Blog { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6RemoveLastOrderByClause");
}
```

EF Core 5.0 翻译的 SQL：

```sql
SELECT [b].[Id], [b].[Name], [t].[Id], [t].[BlogId], [t].[Rating], [t].[Title]
FROM [Blogs] AS [b]
LEFT JOIN (
    SELECT [p].[Id], [p].[BlogId], [p].[Rating], [p].[Title]
    FROM [Posts] AS [p]
    WHERE [p].[Rating] > 3
) AS [t] ON [b].[Id] = [t].[BlogId]
ORDER BY [b].[Id], [t].[Id]
```

EF Core 6.0 翻译的 SQL：

```sql
SELECT [b].[Id], [b].[Name], [t].[Id], [t].[BlogId], [t].[Rating], [t].[Title]
FROM [Blogs] AS [b]
LEFT JOIN (
    SELECT [p].[Id], [p].[BlogId], [p].[Rating], [p].[Title]
    FROM [Posts] AS [p]
    WHERE [p].[Rating] > 3
) AS [t] ON [b].[Id] = [t].[BlogId]
ORDER BY [b].[Id]
```

## 10 用文件名和行号标记查询

从 EF Core 2.2 开始，你可以给你的查询添加一个标签，以达到更好的调试目的。EF Core 6.0 更进一步，现在你可以用 LINQ 代码的文件名和行号来标记查询。

```csharp
using var context = new ExampleContext();
var query = context.Blogs
    .TagWithCallSite()
    .OrderBy(b => b.CreationDate)
    .Take(10)
    .ToQueryString();
Console.WriteLine(query);

class Blog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public DateTime CreationDate { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6TagWithCallSite");
}
```

翻译后的 SQL：

```sql
DECLARE @__p_0 int = 10;

--File: D:\EFCore6\TagWithCallSite\TagWithCallSite\Program.cs:6

SELECT TOP(@__p_0) [b].[Id], [b].[CreationDate], [b].[Name]
FROM[Blogs] AS[b]
ORDER BY[b].[CreationDate]
```

## 11 自有可选从属关系处理

EF Core 6.0 改变了一些对自有可选从属关系的处理。当一个模型有自己的可选从属关系时，EF Core 会在你保存它时警告你所有缺失的属性。

```csharp
using var context = new ExampleContext();
var person = new Person
{
    FirstName = "Oleg",
    LastName = "Kyrylchuk",
    Address = new Address()
};
context.People.Add(person);
await context.SaveChangesAsync();

class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public Address Address { get; set; }
}
class Address
{
    public string City { get; set; }
    public string Street { get; set; }
    public string PostalCode { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Person> People { get; set; }
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<Person>()
            .OwnsOne(p => p.Address);
    }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options
        .EnableSensitiveDataLogging()
        .LogTo(Console.WriteLine)
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6OwnedDependentHandling");
}
```

警告日志：

![](https://img1.dotnet9.com/2022/06/0601.png)

当你有嵌套自有可选从属关系时，EF Core 将不允许创建模型。

```csharp
using var context = new ExampleContext();
var person = new Person
{
   FirstName = "Oleg",
   LastName = "Kyrylchuk",
   ContactInfo = new ContactInfo()
};
context.People.Add(person);
await context.SaveChangesAsync();

class Person
{
   public int Id { get; set; }
   public string FirstName { get; set; }
   public string LastName { get; set; }
   public ContactInfo ContactInfo { get; set; }
}
class ContactInfo
{
   public string Phone { get; set; }
   public Address Address { get; set; }
}
class Address
{
   public string City { get; set; }
   public string Street { get; set; }
   public string PostalCode { get; set; }
}
class ExampleContext : DbContext
{
   public DbSet<Person> People { get; set; }
   protected override void OnModelCreating(ModelBuilder modelBuilder)
   {
       modelBuilder
           .Entity<Person>()
           .OwnsOne(p => p.ContactInfo)
           .OwnsOne(p => p.Address);
   }
   protected override void OnConfiguring(DbContextOptionsBuilder options)
       => options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6OwnedDependentHandling");
}
```

创建模型后将会抛出异常。

这些变化迫使你避免这种情况。你可以通过以下方式解决这些问题。

- 使从属关系成为必需的。
- 在从属关系中至少有一个必需属性。
- 为可选的从属关系创建自己的表，而不是与主体共享它们。

## 12 结尾

本文所有代码示例都可以在我的 GitHub 中找到：[https://github.com/okyrylchuk/dotnet6_features/tree/main/EF%20Core%206#linq-query-enhancements](https://github.com/okyrylchuk/dotnet6_features/tree/main/EF%20Core%206#linq-query-enhancements)

> 原文：https://blog.okyrylchuk.dev/linq-enhancements-in-entity-framework-core-6
>
> 作者：Oleg Kyrylchuk
>
> 翻译：精致码农
