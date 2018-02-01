# Cache 标签

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