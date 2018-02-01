# Cache 标签

## Demo

``` xml
    <Cache Id="T_Test.LruCache"  Type="Lru">
      <Parameter Key="CacheSize" Value="100"/>
      <FlushInterval Hours="0" Minutes="10" Seconds="0"/>
      <FlushOnExecute Statement="T_Test.Insert"/>
      <FlushOnExecute Statement="T_Test.Update"/>
    </Cache>

    <Statement Id="GetList" Cache="T_Test.LruCache">
      SELECT Top 10 T.* From T_Test T With(NoLock)
      <Include RefId="QueryParams"/>
    </Statement>
```

## Cache 标签属性

| 属性       |    说明   |
| :--------- | --------:|
| Id    | 唯一性标号  |
| Type   | Cache类型继承自ICacheProvider,内置常量:Lru 最近最少使用算法,内存缓存, Fifo 先进先出算法,其他继承自ICacheProvider缓存类型均可,例: Type="SmartSql.Cache.Redis.RedisCacheProvider,SmartSql.Cache.Redis" |

## Cache 子标签

| 标签           |    说明   |
| :---------     | --------:|
| FlushInterval  | 定时刷新策略 |
| FlushOnExecute | 事件触发策略 |
| Parameter | 作为 ICacheProvider 初始化参数  |


## FlushInterval

| 属性       |    说明   |
| :--------- | --------:|
| Hours    | 时  |
| Minutes   | 分 |
| Seconds   | 秒 |


## FlushOnExecute

| 属性       |    说明   |
| :--------- | --------:|
| Statement    | 触发刷新缓存的声明  |


## Parameter

| 属性       |    说明   |
| :--------- | --------:|
| Key    | 键  |
| Value    | 值  |
