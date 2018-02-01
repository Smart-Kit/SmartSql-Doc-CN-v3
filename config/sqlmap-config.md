# SmartSqlMapConfig

## Demo

``` xml
<?xml version="1.0" encoding="utf-8" ?>
<SmartSqlMapConfig xmlns="http://SmartSql.net/schemas/SmartSqlMapConfig.xsd">
  <Settings
    IsWatchConfigFile="true"
  />
  <Database>
    <!--ParameterPrefix:[SqlServer:@ | MySQL:? |Oracle::] -->
    <DbProvider Name="SqlClientFactory" ParameterPrefix="@" Type="System.Data.SqlClient.SqlClientFactory,System.Data.SqlClient"/>
    <Write Name="WriteDB" ConnectionString="Data Source=Master.SmartSql.Net;database=SmartSqlDB;uid=sa;pwd=SmartSql.Net"/>
    <Read Name="ReadDB-0" ConnectionString="Data Source=Slave-0.SmartSql.Net;database=TestDB;uid=sa;pwd=SmartSql.net" Weight="80"/>
    <Read Name="ReadDB-1" ConnectionString="Data Source=Slave-1.SmartSql.Net;database=TestDB;uid=sa;pwd=SmartSql.net" Weight="80"/>
  </Database>
  <SmartSqlMaps>
    <SmartSqlMap Path="Maps" Type="Directory"></SmartSqlMap>
  </SmartSqlMaps>
</SmartSqlMapConfig>
```

## Settings 标签属性

| 属性           |    说明   |
| :---------     | --------:|
| IsWatchConfigFile  | 是否监控配置文件,用于配置文件热更新,默认为 false |
| ParameterPrefix | 全局参数前缀,默认使用 $ 适配所有DB |

## Database 子标签

| 标签           |    说明   |
| :---------     | --------:|
| DbProvider  | 各Db Client 的 DbProviderFactory 实现类 |
| Write | 写库 |
| Read | 读库 |

## DbProvider 标签

| 属性           |    说明   |
| :---------     | --------:|
| Name  | 名称标识,可用于**Env**标签的DB环境识别 |
| ParameterPrefix | 参数前缀:[SqlServer:@ ; MySQL:? ; Oracle::] |
| Type | 类型 |

## Write 标签

| 属性           |    说明   |
| :---------     | --------:|
| Name  | 名称标识 |
| ConnectionString | 数据库链接字符串 |

## Read 标签

| 属性           |    说明   |
| :---------     | --------:|
| Name  | 名称标识 |
| ConnectionString | 数据库链接字符串 |
| Weight | 权重 |

## SmartSqlMaps 子标签

| 标签           |    说明   |
| :---------     | --------:|
| SmartSqlMap  | 用于定位SmartSqlMap文件位置 |

## SmartSqlMap 标签

| 属性           |    说明   |
| :---------     | --------:|
| Path  | 用于定位 SmartSqlMap 文件/目录 位置 |
| Type | 枚举类型:File/Directory,File:为文件资源;Directory:为目录资源,取该目录下所有Map文件 |