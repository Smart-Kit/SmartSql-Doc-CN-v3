# Asp.Net Core 最佳实践

## 注入依赖

``` csharp
    services.AddSingleton(sp =>
    {
        var loggerFactory = sp.GetRequiredService<ILoggerFactory>();
        return SmartSql.MapperContainer.Instance.GetSqlMapper(loggerFactory);
    });
```