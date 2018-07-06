# SmartSql.DyRepository

> SmartSql 动态代理仓储，一个高生产力的组件。该组件看似很难懂，实际上仅做了映射Statement,转发请求的功能。但却意义重大。

## 通用泛型仓储接口

> SmartSql.DyRepository.IRepository<TEntity, TPrimary>

``` csharp
    public interface IRepository<TEntity, TPrimary> : IRepository
    {
        int Insert(TEntity entity);
        int Delete(object reqParams);
        [Statement(Id = "Delete")]
        int DeleteById([Param("Id")]TPrimary id);
        int Update(TEntity entity);
        [Statement(Id = "Update")]
        int DyUpdate(object dyObj);
        IEnumerable<TEntity> Query(object reqParams);
        IEnumerable<TEntity> QueryByPage(object reqParams);
        [Statement(Execute = ExecuteBehavior.ExecuteScalar)]
        int GetRecord(object reqParams);
        TEntity GetEntity(object reqParams);
        [Statement(Id = "GetEntity")]
        TEntity GetById([Param("Id")]TPrimary id);
        [Statement(Execute = ExecuteBehavior.ExecuteScalar)]
        bool IsExist(object reqParams);
    }
```