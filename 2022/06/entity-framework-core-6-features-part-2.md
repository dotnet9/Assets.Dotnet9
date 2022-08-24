---
title: EF Core 6 新功能汇总（二）
slug: entity-framework-core-6-features-part-2
description: 继上一篇之后，这一篇将给大家带来另外十个 EF Core 6 中的新功能特性，包括值转换器、脚手架和 DbContext 的改进等。
date: 2022-06-02 21:51:47
copyright: Reprint
author: liamwang 精致码农
originaltitle: EF Core 6 新功能汇总（二）
originallink: https://mp.weixin.qq.com/s/IJd0pwvQhCIohGR0dfekew
draft: False
cover: https://img1.dotnet9.com/2022/06/cover_04.jpg
categories: EF Core
---

继 [上一篇](https://mp.weixin.qq.com/s/VpqEWQPdEJUw_HHNeqBPdg) 之后，这一篇将给大家带来另外十个 EF Core 6 中的新功能特性，包括值转换器、脚手架和 `DbContext` 的改进等。

## 1 HasConversion 支持值转换器

在 EF Core 6.0 中，`HasConversion` 方法的泛型重载方法可以指定内置或自定义的值转换器。

```C#
public class ExampleContext : DbContext
{
    public DbSet<Person> People { get; set; }
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<Person>()
            .Property(p => p.Address)
            .HasConversion<AddressConverter>();
    }
}
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
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

## 2 简化多对多关系的配置

从 EF Core 6.0 开始，你可以在多对多的关系中配置一个连接实体，而无需任何额外的配置。另外，你可以配置一个连接实体，而不需要明确指定左右关系。

```C#
public class BloggingContext : DbContext
{
    public DbSet<Post> Posts { get; set; }
    public DbSet<Tag> Tags { get; set; }
    public DbSet<PostTag> PostTags { get; set; }
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Post>()
            .HasMany(p => p.Tags)
            .WithMany(t => t.Posts)
            .UsingEntity<PostTag>();
    }
    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer("Server=(localdb)\\mssqllocaldb;Database=EFCore6Many2Many;Trusted_Connection=True;");
}
public class Post
{
    public int Id { get; set; }
    public string Name { get; set; }
    public List<Tag> Tags { get; set; } = new List<Tag>();
}
public class Tag
{
    public int Id { get; set; }
    public string Text { get; set; }
    public List<Post> Posts { get; set; } = new List<Post>();
}
public class PostTag
{
    public int PostId { get; set; }
    public int TagId { get; set; }
    public DateTime AddedDate { get; set; }
}
```

## 3 脚手架多对多关系的改进

EF Core 6.0 改进了现有数据库的脚手架。它可以检测到连接表并为其生成多对多的映射。

如下示例数据库：

![](https://img1.dotnet9.com/2022/06/0501.png)

通过 CLI：

```shell
dotnet ef dbcontext scaffold "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=EFCore6Many2Many" Microsoft.EntityFrameworkCore.SqlServer --context ExampleContext --output-dir Models
```

来自生成的 `DbContext` 的 `OnModelCreating`：

```C#
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Post>(entity =>
    {
        entity.HasMany(d => d.Tags)
            .WithMany(p => p.Posts)
            .UsingEntity<Dictionary<string, object>>(
                "PostTag",
                l => l.HasOne<Tag>().WithMany().HasForeignKey("TagId"),
                r => r.HasOne<Post>().WithMany().HasForeignKey("PostId"),
                j =>
                {
                    j.HasKey("PostId", "TagId")
                    j.ToTable("PostTags")
                    j.HasIndex(new[] { "TagId" }, "IX_PostTags_TagId");
                });
    });

    OnModelCreatingPartial(modelBuilder);
}
```

## 4 脚手架生成可空引用类型

EF Core 6.0 改进了现有数据库的脚手架。当项目中启用了可空引用类型（NRT），EF Core 会自动用 NRT 构建 DbContext 和实体类型。

如实例表：

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NOT NULL,
    [Description] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id])
)
```

会生成模型：

```C#
public partial class Post
{
    public int Id { get; set; }
    public string Name { get; set; } = null!;
    public string? Desciption { get; set; }
}
```

## 5 脚手架生成数据库注释

EF Core 6.0 将数据库注释与代码注释关联起来。

数据库例子：

```sql
CREATE TABLE [Posts] (
    [Id] int NOT NULL IDENTITY,
    [Name] nvarchar(max) NOT NULL,
    [Description] nvarchar(max) NULL,
    CONSTRAINT [PK_Posts] PRIMARY KEY ([Id]));
EXEC sp_addextendedproperty
    @name = N'MS_Description', @value = 'The post table',
    @level0type = N'Schema', @level0name = dbo,
    @level1type = N'Table',  @level1name = Posts
EXEC sp_addextendedproperty
    @name = N'MS_Description', @value = 'The post identifier',
    @level0type = N'Schema', @level0name = dbo,
    @level1type = N'Table',  @level1name = Posts,
    @level2type = N'Column', @level2name = [Id];
EXEC sp_addextendedproperty
    @name = N'MS_Description', @value = 'The post name',
    @level0type = N'Schema', @level0name = dbo,
    @level1type = N'Table',  @level1name = Posts,
    @level2type = N'Column', @level2name = [Name];
EXEC sp_addextendedproperty
    @name = N'MS_Description', @value = 'The description name',
    @level0type = N'Schema', @level0name = dbo,
    @level1type = N'Table',  @level1name = Posts,
    @level2type = N'Column', @level2name = [Description];
```

生成的模型：

```C#
/// <summary>
/// The post table
/// </summary>
public partial class Post
{
    /// <summary>
    /// The post identifier
    /// </summary>
    public int Id { get; set; }
    /// <summary>
    /// The post name
    /// </summary>
    public string Name { get; set; }
    /// <summary>
    /// The description name
    /// </summary>
    public string Description { get; set; }
}
```

## 6 AddDbContextFactory 注册 DbContext

在 EF Core 5.0 中，你可以注册一个工厂来手动创建 DbContext 实例。从 EF Core 6.0 开始，可用 `AddDbContextFactory` 注册 `DbContext`。所以你可以根据你的需要同时注入工厂和 DbContext。

```C#
var serviceProvider = new ServiceCollection()
    .AddDbContextFactory<ExampleContext>(builder =>
        builder.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database = EFCore6Playground"))
    .BuildServiceProvider();

var factory = serviceProvider.GetService<IDbContextFactory<ExampleContext>>();
using (var context = factory.CreateDbContext())
{
    // Contexts obtained from the factory must be explicitly disposed
}

using (var scope = serviceProvider.CreateScope())
{
    var context = scope.ServiceProvider.GetService<ExampleContext>();
    // Context is disposed when the scope is disposed
}
class ExampleContext : DbContext
{ }
```

## 7 无依赖性注入的 DbContext 池

在 EF Core 6.0 中，你可以使用没有依赖注入的 `DbContext` 池。`PooledDbContextFactory` 类型已经定义为 `public` 了。池是用 `DbContextOptions` 创建的，它将被用来创建 `DbContext` 实例。

```C#
var options = new DbContextOptionsBuilder<ExampleContext>()
    .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6Playground")
    .Options;

var factory = new PooledDbContextFactory<ExampleContext>(options);

using var context1 = factory.CreateDbContext();
Console.WriteLine($"Created DbContext with ID {context1.ContextId}");
// Output: Created DbContext with ID e49db9b7-a0b0-4b54-8d0d-2cbd6c4cece7:1

using var context2 = factory.CreateDbContext();
Console.WriteLine($"Created DbContext with ID {context2.ContextId}");
// Output: Created DbContext with ID b5a35bcb-270d-40f1-b668-5f76da1f35ad:1

class ExampleContext : DbContext
{
    public ExampleContext(DbContextOptions<ExampleContext> options)
        : base(options)
    {
    }
}
```

## 8 CommandSource 枚举

在 EF Core 6.0 中，新的枚举 `CommandSource` 已经被添加到 `CommandEventData` 类型中，提供给诊断源和拦截器。这个枚举值表明了 EF 的哪个部分创建了这个命令。

在 Db 命令拦截器中使用 `CommandSource`：

```C#
class ExampleInterceptor : DbCommandInterceptor
{
    public override InterceptionResult<DbDataReader> ReaderExecuting(DbCommand command,
        CommandEventData eventData, InterceptionResult<DbDataReader> result)
    {
        if (eventData.CommandSource == CommandSource.SaveChanges)
        {
            Console.WriteLine($"Saving changes for {eventData.Context.GetType().Name}:");
            Console.WriteLine();
            Console.WriteLine(command.CommandText);
        }

        if (eventData.CommandSource == CommandSource.FromSqlQuery)
        {
            Console.WriteLine($"From Sql query for {eventData.Context.GetType().Name}:");
            Console.WriteLine();
            Console.WriteLine(command.CommandText);
        }

        return result;
    }
}
```

`DbContext`:

```C#
class ExampleContext : DbContext
{
    public DbSet<Product> Products { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options
        .UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6CommandSource")
        .AddInterceptors(new ExampleInterceptor());
}
class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}
```

`Program`:

```C#
using var context = new ExampleContext();

context.Products.Add(new Product { Name = "Laptop", Price = 1000 });
context.SaveChanges();

var product = context.Products
    .FromSqlRaw("SELECT * FROM dbo.Products")
    .ToList();

/* Output:
Saving changes for ExampleContext:

SET NOCOUNT ON;
INSERT INTO[Products] ([Name], [Price])
VALUES(@p0, @p1);
SELECT[Id]
FROM[Products]
WHERE @@ROWCOUNT = 1 AND[Id] = scope_identity();


From Sql query for ExampleContext:

SELECT* FROM dbo.Products
*/
```

## 9 值转换器允许转换空值

在 EF Core 6.0 中，值转换器允许转换空值。当你有一个未知值的枚举，并且它在表中表示一个可空的字符串列时，这很有用。

```C#
public class ExampleContext : DbContext
{
    public DbSet<Dog> Dogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder
            .Entity<Dog>()
            .Property(c => c.Breed)
            .HasConversion<BreedConverter>();
    }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder
            .EnableSensitiveDataLogging()
            .LogTo(Console.WriteLine)
            .UseSqlServer("Server=(localdb)\\mssqllocaldb;Database=EFCore6ValueConverterAllowsNulls;");
    }
}
public enum Breed
{
    Unknown,
    Beagle,
    Bulldog
}
public class Dog
{
    public int Id { get; set; }
    public string Name { get; set; }
    public Breed? Breed { get; set; }
}
public class BreedConverter : ValueConverter<Breed, string>
{
#pragma warning disable EF1001
    public BreedConverter()
        : base(
            v => v == Breed.Unknown ? null : v.ToString(),
            v => v == null ? Breed.Unknown : Enum.Parse<Breed>(v),
            convertsNulls: true)
    {
    }
#pragma warning restore EF1001
}
```

但要注意，这里面有陷阱。详情请见链接：[https://docs.microsoft.com/en-us/ef/core/what-is-new/ef-core-6.0/whatsnew#allow-value-converters-to-convert-nulls](https://docs.microsoft.com/en-us/ef/core/what-is-new/ef-core-6.0/whatsnew#allow-value-converters-to-convert-nulls)

## 10 明确设置临时值

在 EF Core 6.0 中，你可以在实体被追踪之前显式地给它们设置临时值。当值被标记为临时值时，EF 将不会像以前那样重置它。

```C#
using var context = new ExampleContext();

Blog blog = new Blog { Id = -5 };
context.Add(blog).Property(p => p.Id).IsTemporary = true;

var post1 = new Post { Id = -1 };
var post1IdEntry = context.Add(post1).Property(e => e.Id).IsTemporary = true;
post1.BlogId = blog.Id;

var post2 = new Post();
var post2IdEntry = context.Add(post2).Property(e => e.Id).IsTemporary = true;
post2.BlogId = blog.Id;

Console.WriteLine($"Blog explicitly set temporary ID = {blog.Id}");
Console.WriteLine($"Post 1 explicitly set temporary ID = {post1.Id} and FK to Blog = {post1.BlogId}");
Console.WriteLine($"Post 2 generated temporary ID = {post2.Id} and FK to Blog = {post2.BlogId}");

// Output:
// Blog explicitly set temporary ID = -5
// Post 1 explicitly set temporary ID = -1 and FK to Blog = -5
// Post 2 generated temporary ID = -2147482647 and FK to Blog = -5

class Blog
{
    public int Id { get; set; }
}
class Post
{
    public int Id { get; set; }
    public int BlogId { get; set; }
}
class ExampleContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }
    public DbSet<Post> Posts { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder options)
        => options.UseSqlServer(@"Server=(localdb)\mssqllocaldb;Database=EFCore6TempValues");
}
```

## 11 结尾

本文所有代码示例都可以在我的 GitHub 中找到：[https://github.com/okyrylchuk/dotnet6_features/tree/main/EF%20Core%206#miscellaneous-enhancements](https://github.com/okyrylchuk/dotnet6_features/tree/main/EF%20Core%206#miscellaneous-enhancements)

>原文：https://blog.okyrylchuk.dev/entity-framework-core-6-features-part-2
>
>作者：Oleg Kyrylchuk
>
>翻译：精致码农