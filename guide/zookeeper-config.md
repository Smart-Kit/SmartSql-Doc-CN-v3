# Zookeeper 分布式配置

## Demo
``` csharp
            string connStr = "192.168.31.103:2181";
            var configLoader = new ZooKeeperConfigLoader(connStr);
            string configPath = "/Config/App1/SmartSqlMapConfig.xml";
            var sqlMapper = new SmartSqlMapper(configPath, configLoader);
            var list = sqlMapper.Query<T_Test>(new RequestContext
            {
                Scope = "T_Test",
                SqlId = "GetList",
                Request = new { Ids = new long[] { 1, 2, 3, 4 } }
            });
```

## 工具使用 [ZooKeeper-Admin](https://github.com/Ahoo-Wang/ZooKeeper-Admin)
![ZooKeeper-Admin](../imgs/zookeeper-config.png)
