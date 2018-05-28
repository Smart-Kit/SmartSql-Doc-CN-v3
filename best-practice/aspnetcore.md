# Asp.Net Core 最佳实践

## 安装 SmartSql.DIExtension

``` chsarp
Install-Package SmartSql.DIExtension
```

## 注入依赖

``` csharp
 services.AddSmartSql();
 services.AddRepositoryFactory();
 services.AddRepositoryFromAssembly((options) =>
 {
    options.AssemblyString = "SmartSql.Starter.Repository";
 });
```

## 定义仓储接口

