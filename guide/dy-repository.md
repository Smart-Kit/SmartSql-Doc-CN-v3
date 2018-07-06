# SmartSql.DyRepository

> SmartSql 动态代理仓储，一个高生产力的组件。该组件看似很难懂，实际上仅做了映射Statement,转发请求的功能。但却意义重大。

## SmartSql提供了一个通用泛型仓储接口

> SmartSql.DyRepository.IRepository<TEntity, TPrimary>

``` csharp
    public interface IRepository<TEntity, TPrimary> : IRepository
    {
        int Insert(TEntity entity);
        int Delete(object reqParams);
        [Statement(Id = "Delete")]
        int DeleteById([Param("Id")]TPrimary id);
        int Update(TEntity entity);
        [Statement(Id = "Update")]
        int DyUpdate(object dyObj);
        IEnumerable<TEntity> Query(object reqParams);
        IEnumerable<TEntity> QueryByPage(object reqParams);
        [Statement(Execute = ExecuteBehavior.ExecuteScalar)]
        int GetRecord(object reqParams);
        TEntity GetEntity(object reqParams);
        [Statement(Id = "GetEntity")]
        TEntity GetById([Param("Id")]TPrimary id);
        [Statement(Execute = ExecuteBehavior.ExecuteScalar)]
        bool IsExist(object reqParams);
    }
```

## 定义仓储接口

``` csharp
    /// <summary>
    /// 属性可选： [SqlMap(Scope = "User")] ,不设置 则默认 Scope 模板：I{Scope}Repository
    /// 可传入自定义模板
    /// RepositoryBuilder builder=new RepositoryBuilder("I{Scope}DAL");
    /// </summary>
    public interface IUserRepository : IRepository<User, string>
    {
        /// <summary>
        /// 属性可选 [Statement(Execute = ExecuteBehavior.Auto,Id = "Query")]
        /// 默认 Execute：Auto ，自动判断 执行类型
        /// 默认 Id : 方法名
        /// </summary>
        /// <param name="reqParams"></param>
        /// <returns></returns>
        [Statement(Sql = "Select Top(@taken) T.* From User T With(NoLock);")]
        IEnumerable<User> QueryBySql(int taken);
    }
```

## 注入依赖

> 注入依赖后将动态实现仓储接口，并映射好相应SqlMap

``` csharp
    services.AddSmartSql();
    services.AddRepositoryFactory();
    services.AddRepositoryFromAssembly((options) =>
    {
        options.AssemblyString = "SmartSql.Starter.Repository";
    });
```

## Attribute

### 仓储接口，函数特性（StatementAttribute）

``` csharp
    public class StatementAttribute : Attribute
    {
        /// <summary>
        /// 定义 SmartSqlMap.Scope 该属性可选，默认使用仓储接口的Scope
        /// </summary>
        public string Scope { get; set; }
        /// <summary>
        /// 可选，默认使用函数名作为 Statement.Id
        /// </summary>
        public string Id { get; set; }
        /// <summary>
        /// 可选， 默认 Execute：Auto ，自动判断 执行类型
        /// </summary>
        public ExecuteBehavior Execute { get; set; } = ExecuteBehavior.Auto;
        /// <summary>
        /// 可选，当不使用 SmartSqlMap.Statement 时可直接定义 Sql
        /// </summary>
        public string Sql { get; set; }
    }
```

``` csharp
    public enum ExecuteBehavior
    {
        Auto = 0,
        Execute = 1,
        ExecuteScalar = 2,
        Query = 3,
        QuerySingle = 4,
        GetDataTable = 5,
        GetDataSet = 6
    }
```

### 仓储接口特性（SqlMapAttribute）

``` csharp
    public class SqlMapAttribute : Attribute
    {
        /// <summary>
        /// SmartSqlMapConfig.Scope 映射
        /// </summary>
        public string Scope { get; set; }
    }
```

### 函数参数特性（ParamAttribute）

``` csharp
    public class ParamAttribute : Attribute
    {
        public ParamAttribute(string name = "")
        {
            Name = name;
        }
        /// <summary>
        /// DbDataParameter.Name
        /// </summary>
        public String Name { get; set; }
```