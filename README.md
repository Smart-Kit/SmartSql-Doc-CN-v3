# 简介

## 0. Why ?
- 拥抱 跨平台 DotNet Core，是时候了。
- 基于Dapper，不再重复造轮子。Dapper性能你懂的。
---
## 1. So SmartSql
 - TargetFrameworks: .NETFramework 4.6 & .NETStandard 1.3 & .NETStandard 2.0
 - SmartSql = Dapper + MyBatis + Cache(Memory | Redis) + ZooKeeper + R/W Splitting + ...... 
----

## 2. 主要特性 (√ 为已完成，未打 √ 为计划特性)
- 1 ORM
  - 1.1 Sync
  - 1.2 Async
- 2 XmlConfig & XmlStatement -> Sql
  - 2.1 SmartSqlMapConfig & SmartSqlMap √  (是的，你猜对了，和MyBatis一样，通过XML配置分离SQL。)
  - 2.2 Config Hot Update ->ConfigWatcher & Reload (配置文件热更新：当你需要修改Sql的时候，直接修改SqlMap配置文件，保存即可。)
- 3 读写分离 √
  - 3.1 读写分离 √
  - 3.2 多读库 权重筛选 √ （配置多读库，根据读库权重选举读库）
- 4 日志 √
  - 4.1 基于 Microsoft.Extensions.Logging.Abstractions  (当你需要跟踪调试的时候一切都是那么一目了然)
- 5 DAO
  - 5.1 DAO  √
  - 5.2 DAO Tool
    - 5.2.1 Template Xml & Entity & DAO  √
    - 5.2.2 Generate Tool
- 6 查询缓存  √
  - 6.1 SmartSql.Cache.Memory  √
      - 6.1.1 Fifo  √
      - 6.1.2 Lru  √
  - 6.2 SmartSql.Cache.Redis  √
  - 6.3 缓存事务一致性
- 7 分布式配置插件
  - 7.1 IConfigLoader √ (配置文件加载器)
  - 7.2 LocalFileConfigLoader  √ √ (本地文件配置加载器)
      - 7.2.1 Load SmartSqlMapSource Xml  √
      - 7.3.1 Load SmartSqlMapSource Directory √
  - 7.3 SmartSql.ZooKeeperConfig √ (ZooKeeper 分布式配置文件加载器)
----

## 3. 性能 
### 查询次数(未开启缓存):100W 

| ORM | Total\(ms\) |
| --- | :---: |
| SmartSql | 63568 |
| Dapper | 60023 |
| MyBaits | 83566 |

### 查询次数(未开启缓存):10W

| ORM | Total\(ms\) |
| --- | :---: |
| SmartSql | 6075 |
| Dapper | 5931 |
| MyBaits | 6574 |
----

## 4. 安装 (NuGet)
```
Install-Package SmartSql
```
## 5. 常规代码
### 查询
``` CSharp
            ISmartSqlMapper SqlMapper = MapperContainer.Instance.GetSqlMapper();
            SqlMapper.Query<T_Test>(new RequestContext
            {
                Scope = "T_Test",
                SqlId = "GetList",
                Request = new { Ids = new long[] { 1, 2, 3, 4 } }
            });
```
### 事务 
``` CSharp
            try
            {
                ISmartSqlMapper SqlMapper = MapperContainer.Instance.GetSqlMapper();
                SqlMapper.BeginTransaction();
                SqlMapper.Execute(new RequestContext
                {
                    Scope = "T_Test",
                    SqlId = "Add",
                    Request = new T_Test { }
                });
                SqlMapper.Execute(new RequestContext
                {
                    Scope = "T_Test",
                    SqlId = "Update",
                    Request = new T_Test { }
                });
                SqlMapper.CommitTransaction();
            }
            catch (Exception ex)
            {
                SqlMapper.RollbackTransaction();
                throw ex;
            }
```
## 6. 技术交流

点击链接加入QQ群【SmartSql 官方交流群】：[604762592](https://jq.qq.com/?_wv=1027&k=5Sy8Ahw)