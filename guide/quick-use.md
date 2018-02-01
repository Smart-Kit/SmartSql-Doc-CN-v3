# 快速使用
## 模板文件

### SmartSqlMapConfig
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

### SmartSqlMap
``` xml

```