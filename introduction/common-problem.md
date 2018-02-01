# 常见问题

## 为什么不支持 Linq?
SmartSql 希望 开发人员更多的接触 Sql ,获得绝对的控制权与安全感.所以目前没有计划支持 Linq 编程模式.

## 对于简单CURD配置
SmartSql 提供了常规Sql操作的模板,开发人员可以直接通过模板生成Xml配置文档.

## 为什么选择Xml作为配置语法
Xml 的标签语法用起来会比较灵活. 比如 筛选标签,嵌套复用,可读性.
复杂查询场景用起来会非常舒爽,可读性也会比较好,代码层面不需要做任何处理,全部交给Xml .

## 集群场景如何使用获得缓存一致性?

SmartSql提供了Redis缓存插件,可通过NuGet获得:
```
Install-Package SmartSql.Cache.Redis
```
##集群场景中Sql配置很多Copy怎么办?

SmartSql 提供了 ZooKeeper配置插件,通过NuGet获得:
```
Install-Package SmartSql.ZooKeeperConfig
```
- 另外作者还开源了ZooKeeper节点管理工具:ZooKeeper-Admin,可直接通过该管理工具直接配置. 关于[ZooKeeper-Admin](https://github.com/Ahoo-Wang/ZooKeeper-Admin):
- 使用 Asp.net Core 编写
- Docker安装:
```
docker pull ahoowang/zookeeper.admin
docker run --name zooAdmin -p 80:80 ahoowang/zookeeper.admin
```
- 本地安装
```
git clone https://github.com/Ahoo-Wang/ZooKeeper-Admin.git
dotnet run
```
## 我之前使用过MyBatis,感觉SmartSql有种似曾相识的感觉?
是的,SmartSql编程模型多处借鉴了MyBatis,如果你之前使用过MyBatis,那么SmartSql将是你更好的选择,因为你几乎不需要有任何编程方式的转变就可以直接上手,并且拥有跨平台,高性能,以及众多你插件的支持.

## 手写Xml 很痛苦是否有智能提示
SmartSql 提供了Xml架构文档,可以获得输入智能提示的体验:
![智能提示](../imgs/intellisense.png)