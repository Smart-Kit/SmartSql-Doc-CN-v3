# SmartSql.TypeHandler

## Nuget 安装

``` cmd
Install-Package SmartSql.TypeHandler -Version 3.0.1
```

SmartSql.TypeHandler 包括了俩种类型的类型处理程序: JsonTypeHandler & XmlTypeHandler，当字段类型需要序列化成Json或Xml场景时可使用以下配置,即可将Data属性序列化成Json字符串存储到DB：

### SmartSqlMapConfig 子元素

``` xml
  <TypeHandlers>
    <TypeHandler Name="Json" Type="SmartSql.TypeHandler.JsonTypeHandler,SmartSql.TypeHandler"/>
    <TypeHandler Name="Xml" Type="SmartSql.TypeHandler.XmlTypeHandler,SmartSql.TypeHandler"/>
  </TypeHandlers>
```

### SmartSqlMap 子元素

``` xml
  <ResultMaps>
    <ResultMap Id="ExtendedDataResult">
      <Result Property="Data" Column="Data" TypeHandler="Json"/>
    </ResultMap>
  </ResultMaps>
  <ParameterMaps>
    <ParameterMap Id="ExtendedDataParameter">
      <Parameter Property="Data" TypeHandler="Json"/>
    </ParameterMap>
  </ParameterMaps>
```