---
seo_title: 动态代理仓储配置
---

# DyRepository配置

DyRepository的配置分为默认配置、特性配置和注册配置，但是都必须配置IoC注册，因为要都需要创建动态的接口实现到IoC中。

## 必须的配置:

1. 单个注册
```cs
    services.AddRepository<IUserRepository>();
```

2. 批量注册
```cs
    services.AddSmartSqlRepositoryFromAssembly((options) =>
    {
        //仓储接口所在程序集全名
        options.AssemblyString = "SmartSql.Starter.Repository";
    });
```

## 可选配置

特性配置指在接口上标注特性来配置DyRepository的配置项，而注册配置是指在IoC注册方法中配置，下面演示一下两者的不同。

### Scope配置

#### 场景

I{Scope}Repository是默认配置的Scope模版，如IUserRepository的Scope就是User。如果是这样的接口命名风格则无需再配置。
而当需要换接口命名风格，如查询User的Dao层名称是IUserDao，则需要配置对应的Scope。

#### 特性配置

```cs
    [SqlMap(Scope = "User")]
    public interface IUserDao
    {
    }
```

#### 注册配置

```cs
    //接口还是那个接口
    public interface IUserDao
    {
    }

    //IoC配置中，注册单个接口
    services.AddRepository<IUserDao>(scope:"User");
    //或批量注册
    services.AddSmartSqlRepositoryFromAssembly((options) =>
    {
        options.AssemblyString = "SmartSql.Starter.Repository";
        options.ScopeTemplate = "I{Scope}Dao";
    });
```

注意，AddSmartSqlRepositoryFromAssembly是可以配置多次的，只要被扫描到的接口不同，就可以给不同的接口配置不同的属性

### SqlId配置

**SqlId默认是取仓储接口的方法名，只要方法名跟xml中的SqlId一样，则无需任何配置。**

#### 场景

因为SmartSql的sql配置是可以动态渲染的，当同一个SqlId传入不同的参数，可以渲染出不同的查询条件。例如：

```xml
    <Statement Id="Query">
      SELECT * FROM User T
      <Where>
        <IsNotEmpty Prepend="And" Property="Email">
          T.Email = @Email
        </IsNotEmpty>
        <IsNotEmpty Prepend="And" Property="UserName">
          T.UserName Like Concat('%',$UserName,'%')
        </IsNotEmpty>
      </Where>
    </Statement>
```

此时如果只用默认配置，写两个Query(string)方法就会有同方法名同参数类型而无法重载的问题。因此，此时需要接口方法名不同，而通过配置去指定相同的SqlId。

#### 特性配置

```cs
    [SqlMap(Scope = "User")]
    public interface IUserRepository
    {
        [Statement(Id = "Query")]
        int QueryByEmail(string email);

        [Statement(Id = "Query")]
        int QueryByUserName(string userName);
    }
```

#### 注册配置

注册配置中是通过配置一个叫sqlIdNamingConvert的委托参数来实现接口方法名到SqlId的转换方法。

```cs
    services.AddSmartSqlRepositoryFactory(sqlIdNamingConvert: (type, method) =>
    {
        if (method.Name.StartsWith("QueryBy"))
            return "Query"; //返回的就是SqlId
    });
    services.AddRepositoryFromAssembly((options) =>
    {
        options.AssemblyString = "SmartSql.Starter.Repository";
    })
```

需要注意的是，这个配置需要把AddSmartSqlRepositoryFactory和AddRepositoryFromAssembly两个方法分开，原因是前几个配置中的AddSmartSqlRepositoryFromAssembly方法内部调用过AddSmartSqlRepositoryFactory，如果再次调用会造成冲突。

### 其它配置

#### 场景

如果希望SmartSql只做接口实现而不侵入接口，以上的注册配置基本就能满足大部分需求。

但是如果需要深入使用SmartSql，那么利用特性配置和一个泛型接口能得到更多额外的功能。

#### 接口方法指定Sql

即直接给接口方法绑定sql，无需再从xml中配置sql了，但请注意参数前缀还是需要在对应的配置文件配置。

```cs
    [Statement(Sql = "Select Top(@taken) T.* From User T With(NoLock);")]
    IEnumerable<User> QueryBySql(int taken);
```

Statement特性只标记在方法上，还有其他几个参数：

|  参数 | 默认值| 说明|
| :---: | :---: |:---:|
| Scope | 当前接口的Scope|对应xml的Scope|
| Id    | 方法名| xml对应Statement的Id|
| Execute |ExecuteBehavior.Auto|执行类型，一般默认就好|
| Sql| 无| 配置Sql后会直接执行这个特性上的Sql|

#### 指定查询参数

即把接口方法的参数值传递给Sql渲染时指定参数名的参数，例如把id的值传递给@UserId：

```cs
    IEnumerable<User> Query([Param("UserId")]int id);
```

#### 泛型接口

继承泛型接口之后，能够直接调用它里面的CURD通用方法。

1. 同步调用：`IRepository<TEntity, TPrimary>`
2. 异步调用：`IRepositoryAsync<TEntity, TPrimary>`