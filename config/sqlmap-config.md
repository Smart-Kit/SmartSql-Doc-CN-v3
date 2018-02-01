# SmartSqlMapConfig 标签

## Settings 标签属性

| 属性           |    说明   |
| :---------     | --------:|
| IsWatchConfigFile  | 是否监控配置文件,用于配置文件热更新,默认为 false |
| ParameterPrefix | 全局参数前缀,默认使用 $ 适配所有DB |

``` xml
  <Settings
    IsWatchConfigFile="true"
    ParameterPrefix="$"
  />
```

## Database 子标签

| 标签           |    说明   |
| :---------     | --------:|
| DbProvider  | 是否监控配置文件,用于配置文件热更新,默认为 false |
| Write | 全局参数前缀,默认使用 $ 适配所有DB |
| Read | 全局参数前缀,默认使用 $ 适配所有DB |

``` xml
  <Database>
    <!--ParameterPrefix:[SqlServer:@ | MySQL:? |Oracle::] -->
    <DbProvider Name="SqlClientFactory" ParameterPrefix="@" Type="System.Data.SqlClient.SqlClientFactory,System.Data.SqlClient"/>
    <Write Name="WriteDB" ConnectionString="Data Source=Master.SmartSql.Net;database=SmartSqlDB;uid=sa;pwd=SmartSql.Net"/>
    <Read Name="ReadDB-0" ConnectionString="Data Source=Slave-0.SmartSql.Net;database=TestDB;uid=sa;pwd=SmartSql.net" Weight="80"/>
    <Read Name="ReadDB-1" ConnectionString="Data Source=Slave-1.SmartSql.Net;database=TestDB;uid=sa;pwd=SmartSql.net" Weight="80"/>
  </Database>
```

## SmartSqlMaps 子标签

| 标签           |    说明   |
| :---------     | --------:|
| SmartSqlMap  | 用于定位SmartSqlMap文件位置 |
