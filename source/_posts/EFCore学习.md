---
title: EFCore学习
date: 2022-07-27 13:30:43
tags: 
- C# 
- EFCore
---



# EFCore学习

[TOC]

## 一、项目准备

### 1、添加相关Nuget包

{% https://gitee.com/jason-xiang/blog-img/raw/master/pic/2022/09/09_165419.png  [Nuget包] %}

### 2、创建项目

创建名为Demo的空的解决方案。

创建Demo.app控制台项目。Demo.Data和Demo.Domain的类库

https://gitee.com/jason-xiang/blog-img/raw/master/pic/2022/09/09_165347.png

### 3、编写实体类

```c#
public class Club
{
    public Club()
    {
        Players = new List<Player>();
    }
    public int Id { get; set; }
    public string Name { get; set; }
    public string City { get; set; }
    //规定这一列为Date格式
    [Column(TypeName = "date")]
    public DateTime DateofEstablishment { get; set; }
    public string History { get; set; }
    public League League { get; set; }
    public List<Player> Players { get; set; }
}
public class Game
{
    public Game() {
        GamePlayers = new List<GamePlayer>();
    }
    public int Id { get; set; }
    public int Round { get; set; }
    public DateTimeOffset? StartTime { set; get; }
    public List<GamePlayer> GamePlayers { get; set; }
}
public class League
{
    public int Id { get; set;}
    //非空 长度为100
    [Required,MaxLength(100)]
    public string Name { get; set; }
    //非空 长度为100
    [Required, MaxLength(100)]
    public string Country { get; set; }
}
public class Player
{
    public Player() {
        GamePlayers = new List<GamePlayer>();
    }

    public int Id { get; set; }
    public string Name { get; set; }

    [Column(TypeName = "date")]
    public DateTime DateofBirth { get; set; }

    public List<GamePlayer> GamePlayers { get; set; }

    public int ResumeId { get; set; }
    public Resume Resume { get; set; }
}
public class Resume
{
    public int Id { get; set; }
    public string Description { get; set; }
    public int PlayerId { get; set; }
    public Player Player { get; set; }
}
```

4、在Demo.Date添加数据库配置类

```c#
public class DemoContext :DbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    { 
        optionsBuilder
            //配置日志工厂
            .UseLoggerFactory(ConsoleLoggerFactory)
            // 输出sql语句参数
            .EnableSensitiveDataLogging()
            .UseMySql("server=127.0.0.1;port=3306;database=football;userid=root;password=123456");
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        //配置多对多外键
        modelBuilder.Entity<GamePlayer>().HasKey(x => new { x.PlayerId, x.GameId  });
        //配置一对一 主键
        modelBuilder.Entity<Resume>().HasOne(x => x.Player)
            // 配置 被约束表
            .WithOne(x => x.Resume)
            .HasForeignKey<Resume>(x => x.PlayerId);
    }
    //添加sql语句输出工厂类（类似于  @Bean）
    public static readonly ILoggerFactory ConsoleLoggerFactory = LoggerFactory.Create(builder =>
    {
        builder.AddFilter((category, level) =>
        category == DbLoggerCategory.Database.Command.Name && level == LogLevel.Information).AddConsole(); ;
    });

    public DbSet<League> Leagues { get; set; }
    public DbSet<Club> Clubs { get; set; }
    public DbSet<Player> Players { get; set; }

}
```

### 5、在Nuget管理控制台输入相关命令，生成数据表

```DOS
输入：
Add-Migration [名字]

Update-Datebase
```

![](https://gitee.com/jason-xiang/blog-img/raw/master/pic/2022/09/09_165504.png)

## 二、具体操作

### 基础操作

#### 1、增加

增加一共有三种方式添加，通过调用DbContext中的 Add

```c#
var SerieA = new League
{
    Country = "Italy",
    Name = "Serie A"
};
context.Leagues.Add(SerieA);

var SerieB = new League { Name = "Serie B", Country = "Italy" };
var SerieC = new League { Name = "Serie C", Country = "Italy" };
context.Leagues.AddRange(new List<League> { SerieB, SerieC });

var count = context.SaveChanges();
Console.WriteLine(count);

```

#### 2、查询

查询一共两种方式查询：

- 通过Linq方法.ToList()，EFCore遇到ToList()语句就会执行SQL查询

```c#
var leagues = context.Leagues
    .where(x => x.Country == "Italy")
    .ToList();
```

- Linq查询表达式，查询结果与Linq方法是一样的(类似于sql查询语句)

```c#
var leagues2 = (from lg in context.leagues
                where lg.country == "italy"
                select lg).tolist();

```

**延迟执行**

在.ToList()方法之前都是返回IQueryable类型

> IQueryable就是C#中Linq To SQL语句的返回类型，简单来说IQueryable就是可以叠加处理SQL语句，最后统一访问数据库，这个处理过程就叫`延迟执行`，这一步只是生成了SQL语句，并没有真正执行数据库查询

查询方法

>常用的查询方法（看帮助文档）：
>  ToList()，返回集合
>  First()，返回（符合条件的第）一个数据，必须有一个数据，没有数据就会报错，方法中可以直接写条件，不需要Where()
>  FirstOrDefaule()，返回（符合条件的第）一个数据，可以没有数据，方法中可以直接写条件，不需要Where()
>  Single()，返回序列的唯一元素；如果该序列并非恰好包含一个元素，则会引发异常。
>  SingleOrDefault()，返回序列中的唯一元素；如果该序列为空，则返回默认值；如果该序列包含多个元素，此方法将引发异常。
>  Last()
>  LastOrDefaule()
>实际应用中，经常使用OrDefault的方法
>
>Find()，根据主键查找，属于DbSet的方法
>
>Count()
>LongCount()
>Min()
>Max()
>Average()
>Sum()
>
>还有异步版本：`ToListAsync()`、`FirstAsync()`

**针对主键查询**

只会生成一次sql语句,因为context会追踪查询出来的数据，Find()再执行时，如果context能在内存里找到数据，那么就不会去数据库查询

只有Find()方法会这样执行



**模糊查询**
比如需要模糊sql查询条件

```C#
SELECT * FROM Leagues WHERE Country LIKE N'%中%'
    
    
//第一种 模糊查询
var leagues = _dbContext.Leagues
    .Where(l => l.Country.Contains("中"))//查询条件
    .ToList();
//第二种 模糊查询
var league_ef = _dbContext.Leagues
    .Where(l => EF.Functions.Like(l.Country, "中%"))
    .ToList();
```

**查询单个**

```c#
 //第一种
var leagues = _dbContext.Leagues.SingleOrDefault(l => l.Id == _id);
 //第二种
var league2 = _dbContext.Leagues.Find(_id);
```

#### 3、删除

删除时，只能删除被追踪的数据。

一共用4中删除方法

```c#
using var context = new DemoContext();

var milan = context.Clubs.Single(x => x.Name == "AC Milan");
//1
context.Clubs.Remove(milan);
//2
context.Remove(milan);
//3
context.Clubs.RemoveRange(milan, milan);
//4
context.RemoveRange(milan,milan);

var count = context.SaveChanges();
Console.WriteLine(count);
```

#### 4、更新

更新和删除一样只能对被追踪的数据进行修改。如果需要对没有被追踪的数据进行修改，需要调用context.Update（）方法

```c#
        public static void UpdateOne()
        {
            using var context = new DemoContext();
            var league = context.Leagues.Where(x => x.Name == "Serie B").FirstOrDefault();
            Object count = null;
            if (league != null)
            {
                league.Name += "~~";
                count = context.SaveChanges();
            }
            Console.WriteLine(count);
        }
        public static void UpdateAll()
        {
            using var context = new DemoContext();
            //AsNoTracking 不进行追踪
            var Leagues = context.Leagues
                .AsNoTracking()
                .Skip(1)
                .Take(2).ToList();
            Leagues.ForEach(league => league.Name += "++");
            context.Leagues.UpdateRange(Leagues) ;
            var count = context.SaveChanges();
            Console.WriteLine(count);
        }
```

### 高级操作

#### 一对多查询

一对多查询需要用到include 的函数

```c#
public static void GetOneToMany() {
    
}
```



**多表查询**

| 方法   | 有主键                   | 没有主键 |
| ------ | ------------------------ | -------- |
| Add    | 添加数据（主键不能重复） | 添加数据 |
| Update | 修改数据库中的数据       | 添加数据 |
| Attach | 不变化                   | 添加数据 |
