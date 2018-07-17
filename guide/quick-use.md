# 快速使用

## 安装 XML Schema File 文件获得智能提示

下载以下俩个文件至 Microsoft Visual Studio XSD安装目录

``` chsarp
VS2017目录地址：\Microsoft Visual Studio\2017\Enterprise\Xml\Schemas
```

| 文件      |   地址   |
| --------  | -----:  |
| SmartSqlMapConfig.xsd  | [SmartSqlMapConfig.xsd](https://raw.githubusercontent.com/Ahoo-Wang/SmartSql/master/doc/Schemas/SmartSqlMapConfig.xsd) |
| SmartSqlMap.xsd        |   [SmartSqlMap.xsd](https://raw.githubusercontent.com/Ahoo-Wang/SmartSql/master/doc/Schemas/SmartSqlMap.xsd)   |

## 模板文件

### SmartSqlMapConfig

``` xml
<?xml version="1.0" encoding="utf-8" ?>
<SmartSqlMapConfig xmlns="http://SmartSql.net/schemas/SmartSqlMapConfig.xsd">
  <Settings IsWatchConfigFile="true" />
  <Database>
    <!--ParameterPrefix:[SqlServer:@ | MySQL:? |Oracle::] -->
    <!--<DbProvider Name="MySqlClientFactory" ParameterPrefix="?" Type="MySql.Data.MySqlClient.MySqlClientFactory,MySql.Data"/>-->
    <!--<DbProvider Name="OracleClientFactory" ParameterPrefix=":" Type="Oracle.ManagedDataAccess.Client.OracleClientFactory,Oracle.ManagedDataAccess"/>-->
    <!--<DbProvider Name="NpgsqlFactory" ParameterPrefix="@" Type="Npgsql.NpgsqlFactory,Npgsql"/>-->
    <DbProvider Name="SqlClientFactory" ParameterPrefix="@" Type="System.Data.SqlClient.SqlClientFactory,System.Data.SqlClient"/>
    <Write Name="WriteDB" ConnectionString="Data Source=.;database=TestDB;uid=sa;pwd=SmartSql.net"/>
    <Read Name="ReadDB-0" ConnectionString="Data Source=.;database=TestDB;uid=sa;pwd=SmartSql.net" Weight="80"/>
    <Read Name="ReadDB-1" ConnectionString="Data Source=.;database=TestDB;uid=sa;pwd=SmartSql.net" Weight="20"/>
  </Database>

  <!--
  <TypeHandlers>
    <TypeHandler Name="Json" Type="SmartSql.TypeHandler.JsonTypeHandler,SmartSql.TypeHandler"/>
    <TypeHandler Name="Xml" Type="SmartSql.TypeHandler.XmlTypeHandler,SmartSql.TypeHandler"/>
  </TypeHandlers>
  -->
  <SmartSqlMaps>
    <SmartSqlMap Path="Maps" Type="Directory"></SmartSqlMap>
    <!--<SmartSqlMap Path="Maps/T_Table.xml"></SmartSqlMap>-->

  </SmartSqlMaps>
</SmartSqlMapConfig>

```

### SmartSqlMap

``` xml
<?xml version="1.0" encoding="utf-8" ?>
<SmartSqlMap Scope="T_Test"  xmlns="http://SmartSql.net/schemas/SmartSqlMap.xsd">
  <Caches>
    <Cache Id="T_Test.LruCache"  Type="Lru">
      <Parameter Key="CacheSize" Value="100"/>
      <FlushInterval Hours="0" Minutes="10" Seconds="0"/>
      <FlushOnExecute Statement="T_Test.Insert"/>
      <FlushOnExecute Statement="T_Test.Update"/>
    </Cache>
  </Caches>
  <Statements>
    <Statement Id="QueryParams">
      <Where>
        <IsNotEmpty Prepend="And" Property="Name">
          T.Name Like Concat('%',@Name,'-%')
        </IsNotEmpty>
      </Where>
    </Statement>
    <!--新增-->
    <Statement Id="Insert">
      INSERT INTO T_Test
      (Name)
      VALUES
      (@Name)
      ;Select Scope_Identity();
    </Statement>
    <!--删除-->
    <Statement Id="Delete">
      Delete T_Test
      Where Id=@Id
    </Statement>
    <!--更新-->
    <Statement Id="Update">
      UPDATE T_Test
      SET
      Name = @Name
      Where Id=@Id
    </Statement>
    <!--获取数据列-->
    <Statement Id="GetList" Cache="T_Test.LruCache">
      SELECT Top 10 T.* From T_Test T With(NoLock)
      <Include RefId="QueryParams"/>
    </Statement>
    <Statement Id="GetListByLruCache" Cache="T_Test.LruCache">
      SELECT Top 10 T.* From T_Test T With(NoLock)
      <Include RefId="QueryParams"/>
    </Statement>
    <!--获取分页数据-->
    <Statement Id="GetListByPage">
      Select TT.* From
      (Select ROW_NUMBER() Over(Order By T.Id Desc) Row_Index,T.* From T_Test T With(NoLock)
      <Include RefId="QueryParams"/>) TT
      Where TT.Row_Index Between ((@PageIndex-1)*@PageSize+1) And (@PageIndex*@PageSize)
    </Statement>
    <!--获取记录数-->
    <Statement Id="GetRecord">
      Select Count(1) From T_Test T With(NoLock)
      <Include RefId="QueryParams"/>
    </Statement>
    <!--获取表映射实体-->
    <Statement Id="GetEntity" Cache="T_Test.LruCache">
      Select Top 1 T.* From T_Test T With(NoLock)
      <Where>
        <IsNotEmpty Prepend="And" Property="Id">
          T.Id=@Id
        </IsNotEmpty>
      </Where>
    </Statement>
    <!--是否存在该记录-->
    <Statement Id="IsExist">
      Select Count(1) From T_Test T With(NoLock)
      <Include RefId="QueryParams"/>
    </Statement>
  </Statements>
</SmartSqlMap>
```

## 代码

### 查询

``` csharp
            ISmartSqlMapper SqlMapper = MapperContainer.Instance.GetSqlMapper();
            SqlMapper.Query<T_Test>(new RequestContext
            {
                Scope = "T_Test",
                SqlId = "GetList",
                Request = new { Name = "SmartSql" }
            });
```

### 事务

``` csharp
            try
            {
                ISmartSqlMapper SqlMapper = MapperContainer.Instance.GetSqlMapper();
                SqlMapper.BeginTransaction();
                //Do Biz
                SqlMapper.CommitTransaction();
            }
            catch (Exception ex)
            {
                SqlMapper.RollbackTransaction();
                throw ex;
            }
```