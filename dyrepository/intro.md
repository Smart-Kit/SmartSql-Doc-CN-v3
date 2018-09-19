---
seo_title: 动态代理仓储介绍
---

# 动态代理仓储

## 简介

动态代理仓储(SmartSql.DyRepository)组件是SmartSql非常独特的功能，它能简化SmartSql的使用。对业务代码除了配置几乎没有侵入。可以说使用SmartSqlContainer是原始方法，而DyRepository自动帮你实现这些方法。

DyRepository的表现是只需要定义仓储接口，通过简单配置就能自动实现这些接口并注册到IoC容器中，使用时注入即刻获取实现。原理是通过接口和接口方法的命名规则来获取SmartSql的xml文件中的Scope和SqlId，用接口方法的参数作为Request，通过xml中的sql自动判断是查询还是执行操作，最后实现对ISmartSqlMapper的调用。

## 适合场景

1. 使用了仓储模式的架构

仓储模式主要在DDD战术中运用，用来隔离领域和数据库。DyRepository的功能需求主要是在DDD的实践中发现的，目前为止已经满足DDD实践的大部分需求，如果还有其他的相关需求欢迎提出Issue。

2. 类似SqlHepler的应用

DyRepository可以将任意一个接口实现出查询数据库的工具，CURD方法不在话下。通过接口注入更能发挥解耦的作用。

## 使用介绍

下面会简单演示DyRepository与ISmartSqlMapper的使用对比。

### 准备工作

1. 先创建一个仓储，这个仓储不依赖SmartSql，只是普普通通的仓储接口
```cs
    //仓储接口，默认模版是I{Scope}Repository，所以这个接口的Scope是Activity
    public interface IActivityRepository
    {
        //接口方法对应SqlId，所以这个方法的SqlId是Insert
        //方法参数对应Request，所以这个方法的Request是activity
        int Insert(Activity activity);

        //值类型的参数会自动封装为一个对象，所以这个方法的Request是new { activityId = activityId }
        Activity Query(long activityId);
    }
    
```

2. 创建配置xml文件SmartSqlMapConfig.xml：

```xml
<?xml version="1.0" encoding="utf-8" ?>
<SmartSqlMapConfig xmlns="http://SmartSql.net/schemas/SmartSqlMapConfig.xsd">
  <Settings IsWatchConfigFile="true" IgnoreParameterCase="true"/>
  <Database>
    <!--ParameterPrefix:[SqlServer:@ | MySQL:? |Oracle::] -->
    <DbProvider Name="SqlClientFactory" ParameterPrefix="@" Type="System.Data.SqlClient.SqlClientFactory,System.Data.SqlClient"/>
    <Write Name="WriteDB" ConnectionString="Data Source=.;Initial Catalog=SmartSqlStarterDB;Integrated Security=True"/>
  </Database>
  <SmartSqlMaps>
    <SmartSqlMap Path="Maps" Type="Directory"></SmartSqlMap>
  </SmartSqlMaps>
</SmartSqlMapConfig>

```

3. 再创建xml配置文件Activity.xml，放到Maps目录，并且在属性面板设置为“始终复制”:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<SmartSqlMap Scope="Activity"  xmlns="http://SmartSql.net/schemas/SmartSqlMap.xsd">
  <Statements>
    <Statement Id="Insert">
      INSERT INTO Activity
      (ActivityId
      ,Name
      ,BeginTime
      ,Address
      ,CreationTime
      ,Deleted)
      VALUES
      (@ActivityId
      ,@Name
      ,@BeginTime
      ,@Address
      ,@CreationTime
      ,@Deleted)
      ;Select Scope_Identity();
    </Statement>

    <Statement Id="Query">
      SELECT * FROM Activity
      WHERE
      ActivityId = @activityId;
    </Statement>
  </Statements>
</SmartSqlMap>
```

准备工作完成，下面就可以展示两种用法的区别。

### 两种用法

#### ISmartSqlMapper 用法

如果不用DyRepository，我们需要用ISmartSqlMapper实现这个仓储。

```cs

    public class ActivityRepository : IActivityRepository
    {
        ISmartSqlMapper SqlMapper = MapperContainer.Instance.GetSqlMapper();

        public int Insert(Activity activity)
        {
            return SqlMapper.ExecuteScalar<int>(new RequestContext()
            {
                Scope = "Activity",
                SqlId = "Insert",
                Request = activity
            });
        }

        public Activity Query(long activityId)
        {
            return SqlMapper.Query<Activity>(new RequestContext()
            {
                Scope = "Activity",
                SqlId = "Query",
                Request = new { activityId = activityId }
            });
        }
    }
```
再把实现类注册到IoC中:

```cs
var services = new ServiceCollection();
var services.AddSingleton<IActivityRepository,ActivityRepository>();
```

#### DyRepository

如果使用DyRepository，我们只需配置一下IoC注册即可。

```cs
    var services = new ServiceCollection();
    services.AddSmartSqlRepositoryFromAssembly((options) =>
    {
        options.AssemblyString = "SmartSql.Starter.Repository";
    });
```

#### 注入使用

使用方法就注入接口，再调用接口方法了。

```cs
    // 假设ActivityService已经注册到IoC容器
    public class ActivityService
    {
        IActivityRepository activityRepository;

        public ActivityService(IActivityRepository activityRepository)
        {
            this.activityRepository = activityRepository;
        }

        public int Create(Activity activity)
        {
            return activityRepository.Insert(activity);
        }

        public int GetById(int id)
        {
            return activityRepository.Insert(id);
        }
    }

```

## 总结

通过DyRepository与ISmartSqlMapper的简单对比，我们就可以看出DyRepository的强大，为我们省下了很多代码。当然，ISmartSqlMapper自然也有它的灵活性，能够在任何地方使用。但是如果没有其他的特殊需求，在架构方面，因为对业务代码几乎无侵入，DyRepository无疑是最推荐的使用方式。

本文只介绍了DyRepository默认约定的使用方法，其实它还能通过各种配置项去实现更灵活的功能。详情请看下一篇《DyRepository配置》。