# Redis 分布式缓存

## Cache 配置
``` xml
<Cache Id="T_Test.RedisCache" Type="SmartSql.Cache.Redis.RedisCacheProvider,SmartSql.Cache.Redis">
  <Parameter Key="CacheSize" Value="1000"/>
  <Parameter Key="ConnectionString" Value="192.168.31.103"/>
  <Parameter Key="DatabaseId" Value="0"/>
  <FlushInterval Hours="24" Minutes="0" Seconds="0"/>
  <FlushOnExecute Statement="T_Test.Insert"/>
  <FlushOnExecute Statement="T_Test.Update"/>
</Cache>
```
## Demo
``` xml
<Statement Id="GetListByRedisCache" Cache="T_Test.RedisCache">
      SELECT Top 10 T.* From T_Test T With(NoLock)
      <Include RefId="QueryParams"/>
    </Statement>
```