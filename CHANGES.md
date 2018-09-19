# SmartSql 更新历史记录

## 3.7.0

1. support cross SqlMap reference for #30
2. modifying Statement.Ref for delay dependence
3. optimize Analyse Statement.SqlCommandType
4. optimize CheckIncludeCyclicDependency
5. fixed MultipleResultMap.Root result Map
6. fixed PreparedCommand Sql log output for IgnoreParameterCase

## 3.6.8

1. add support Statement for Transaction
2. optimize Log output for issues:35
3. add support [.] PropertyAccessor
4. add support alias for SmartSqlOptions

## 3.6.6

1. fixed ObjectUtils key conflicts
2. add support for SmartSqlMapper multiple instance injection
3. add support for IServiceProvider.GetSmartSqlMapper(string configPath)

``` csharp
      var smartSqlMapper = serviceProvider.GetSmartSqlMapper("SmartSql");
      var smartSqlMapper_1 = serviceProvider.GetSmartSqlMapper("SmartSql-1");
```

4. optimize Options DI for SmartSqlOptions.UseOptions

``` csharp
            var builder = new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                 .AddJsonFile("SmartSqlConfig.json", false, true);

            var configuration = builder.Build();
            var services = new ServiceCollection();

            services.AddOptions();
            var smartSqlConfigJson = configuration.GetSection("SmartSqlConfig");
            services.Configure<SmartSqlConfigOptions>("SmartSql", smartSqlConfigJson);

            services.AddSmartSql(sp =>
            {
                return new SmartSqlOptions
                {
                     ConfigPath= "SmartSql"
                }.UseOptions(sp);
            });
```

## 3.6.4

1. added support Root for MultipleResultMap
2. added support ReadDb for Statement
3. optimized PreparedCommand.Prepare log output

## 3.6.3

1. optimized ITransaction Extension
2. default injection SmartSql for AddRepository DI
3. fixed path error for SmartSql.Options
4. fixed SqlCommandAnalyzer
5. add SqlIdNamingConvert

## v3.6.0-rc1

1. add MultipleResultMap
2. add GetNested api
3. add GetNestedAsync api
4. add FillMultiple api
5. add FillMultipleAsync api
6. optimized ValueTuple result type for Repository
7. add support Nested result type for Repository

``` xml
    <MultipleResultMap Id="QueryByPageMReuslt">
      <Result Property="Total"/>
      <Result Property="List"/>
    </MultipleResultMap>
    <Statement Id="MQueryByPage" MultipleResultMap="QueryByPageMReuslt">
      Select Count(1) From T_Entity;
      Select Top 10 T.* From T_Entity T
    </Statement>
```

``` csharp
    public class QueryByPageResponse
    {
        public int Total { get; set; }
        public IEnumerable<T_Entity> List { get; set; }
    }

QueryByPageResponse MQueryByPage();
```

## v3.5.14

1. enhanced Maps.Statement support for CommandType and SourceChoice
2. fixed Tag.For NotDirectValue bug
3. optimized RequestContext.Request is DbParameterCollection

## v3.5.10

1. fixed Generic nested return value bug
2. optimized DyRepository DI register
3. fixed the same naming problems with different repository interfaces
4. enhanced stored procedure support
5. fixed the cache penetration problem with the cache value of null
6. optimized storage procedure call interface construction

## v3.5.3

1. add support ValueTuple result

``` csharp
(int,User) QueryByPage(object reqParams);

(int,T) QueryByPage<T>(object reqParams);
```

## v3.5.2

1. add support SmartSqlMapConfig.SmartSqlMap.Type=DirectoryWithAllSub

## v3.5.1

1. add support QueryMultiple and QueryMultipleAsync
2. SmartSql.DyRepository add support QueryMultiple And QueryMultipleAsync result
3. support change table structure auto Deserializer
4. SmartSql.Options add UserOptions for DI
5. add support Env SmartSqlConfigPath
    - 5.1 Production => SmartSqlMapConfig.xml
    - 5.2 Development => SmartSqlMapConfig.Development.xml
    - 5.3 Staging => SmartSqlMapConfig.Staging.xml

## v3.4.9

1. fix DyRepository ExecuteBehavior.Auto bug

## v3.4.8

1. optimize DyRepository automatic execute for return int
2. optimize default excute DataSourceChoice
3. add support StatementAttribute.CommandType and SourceChoice

## v3.4.3

1. fix sqlmap hot update bug
2. add support multi SmartSqlMapper instance
3. fix Tag.Placeholder Space bug
4. optimize SQL output
5. Support for returning generic type nesting
6. add support IRepositoryAsync
7. add support muti custom scope_template
8. fix Cache.FlushInterval attr bug
9. optimize the resource reading directory

## v3.3.8

- fix GetDataSetAsync to use NextResultAsync
- fix IsStatementSql.Defalut-Value=true
- SmartSql.DyRepository support ParamAttribute
- add SmartSql.Options DI AddSmartSqlOptionLoader & AddSmartSqlOption

## v3.3.6

- add ISession
- add ITransaction
- add ISession to DI
- add ITransaction to DI
- add ISmartSqlMapperAsync to DI

## v3.3.3

- add support ResultMap.Constructor
- add support private ctor entity deser

## v3.3.1

- SmartSql.DyRepository add support asynchronous function
- add support DEBUG-log output SQL And DBParameters.

## v3.2.0

1. add support pure SQL parameters For RequestContext.RealSql
2. add support SmartSql.DyRepository Sql Attribute For StatementAttribute.Sql

> SmartSql.DyRepository Demo

``` csharp
        [Statement(Sql = "Select Top(@Taken) T.* From T_Entity T With(NoLock);")]
        IEnumerable<T_Entity> QueryBySql(int Taken);
```

## v3.1.0

1. add GetDataTable Method
2. add GetDataSet Method
3. add GetDataTableAsync Method
4. add GetDataSetAsync Method

## SmartSql V3 Update

> The lightest ORM in history! **107kb**

### Update content

1. Remove Dapper dependency
2. Support stored procedures
3. Enhanced extensibility
4. Refactoring code
5. Optimal cache trigger strategy
6. Dynamic implementation of Repository interface
7. Support Parameter & Result Map & TypeHandler
8. High performance

### Performance evaluation

``` ini

BenchmarkDotNet=v0.10.14, OS=Windows 10.0.17134
Intel Core i7-6700K CPU 4.00GHz (Skylake), 1 CPU, 8 logical and 4 physical cores
.NET Core SDK=2.1.201
  [Host]     : .NET Core 2.0.7 (CoreCLR 4.6.26328.01, CoreFX 4.6.26403.03), 64bit RyuJIT
  DefaultJob : .NET Core 2.0.7 (CoreCLR 4.6.26328.01, CoreFX 4.6.26403.03), 64bit RyuJIT

```

|            ORM |                     Type |                  Method |        Return |      Mean |     Error |    StdDev | Rank |     Gen 0 |     Gen 1 |     Gen 2 | Allocated |
|--------------- |------------------------- |------------------------ |-------------- |----------:|----------:|----------:|-----:|----------:|----------:|----------:|----------:|
|         Native |         NativeBenchmarks |   Query_GetValue_DbNull | IEnumerable |  78.39 ms | 0.8935 ms | 0.7921 ms |    1 | 3000.0000 | 1125.0000 |  500.0000 |  15.97 MB |
|       SmartSql |       SmartSqlBenchmarks |                   Query | IEnumerable |  78.46 ms | 0.2402 ms | 0.1875 ms |    1 | 2312.5000 | 1000.0000 |  312.5000 |  12.92 MB |
| SmartSqlDapper | SmartSqlDapperBenchmarks |                   Query | IEnumerable|  78.65 ms | 1.2094 ms | 1.1312 ms |    1 | 3687.5000 | 1437.5000 |  687.5000 |  19.03 MB |
|         Native |         NativeBenchmarks | Query_IsDBNull_GetValue | IEnumerable |  78.84 ms | 0.8984 ms | 0.7502 ms |    1 | 2312.5000 | 1000.0000 |  312.5000 |  12.92 MB |
|         Dapper |         DapperBenchmarks |                   Query | IEnumerable |  79.00 ms | 1.0949 ms | 0.9706 ms |    1 | 3312.5000 | 1312.5000 |  625.0000 |  17.19 MB |
|             EF |             EFBenchmarks |                   Query | IEnumerable|  79.44 ms | 1.6880 ms | 1.5789 ms |    1 | 6250.0000 |         - |         - |  26.05 MB |
|       SqlSugar |       SqlSugarBenchmarks |                   Query | IEnumerable |  81.09 ms | 0.8718 ms | 0.7728 ms |    2 | 2187.5000 |  875.0000 |  250.0000 |  12.64 MB |
|          Chloe |          ChloeBenchmarks |                   Query | IEnumerable|  83.86 ms | 1.2714 ms | 1.1893 ms |    3 | 2250.0000 |  937.5000 |  312.5000 |  12.62 MB |
|             EF |             EFBenchmarks |                SqlQuery | IEnumerable |  89.11 ms | 0.7562 ms | 0.6314 ms |    4 | 8187.5000 |  125.0000 |         - |  33.68 MB |
|             EF |             EFBenchmarks |        Query_NoTracking | IEnumerable |  93.13 ms | 0.8458 ms | 0.7912 ms |    5 | 5875.0000 | 2250.0000 | 1062.5000 |  29.71 MB |
|             EF |             EFBenchmarks |     SqlQuery_NoTracking | IEnumerable | 106.89 ms | 1.0998 ms | 1.0288 ms |    6 | 7437.5000 | 2875.0000 | 1312.5000 |  37.34 MB |