# SmartSqlMap

| 属性       |    说明   |
| :--------- | --------:|
| Scope    | 域,用于SqlMap定义Sql声明范围  |

## ResultMap

``` xml
    <ResultMap Id="UserExtendResultMap">
      <Result Column="Data" Property="Info" TypeHandler="Json"/>
    </ResultMap>
```

| 属性       |    说明   |
| :--------- | --------:|
| Id    | 唯一性编号  |

### ResultMap.Result

| 属性       |    说明   |
| :--------- | --------:|
| Column    | 列名  |
| Property    | 属性名  |
| TypeHandler    | 类型处理器，内置（Json/Xml）  |

## ParameterMap

``` xml
    <ParameterMap Id="UserExtendParameterMap">
      <Parameter Property="Info" TypeHandler="Json"/>
    </ParameterMap>
```

| 属性       |    说明   |
| :--------- | --------:|
| Id    | 唯一性编号  |

### ParameterMap.Parameter

| 属性       |    说明   |
| :--------- | --------:|
| Property    | 属性名  |
| TypeHandler    | 类型处理器，内置（Json/Xml）  |

## MultipleResultMap

``` xml
    <MultipleResultMap Id="QueryByPage_Map">
      <Result  Property="Total"/>
      <Result  Property="List"/>
    </MultipleResultMap>
```

``` csharp
    public class QueryByPageResponse
    {
        public int Total { get; set; }
        public IEnumerable<T_Entity> List { get; set; }
    }
```

| 属性       |    说明   |
| :--------- | --------:|
| Id    | 唯一性编号  |

### MultipleResultMap.Result

| 属性       |    说明   |
| :--------- | --------:|
| Property    | 属性  |

## Statement标签

| 属性       |    说明   |
| :--------- | --------:|
| Id    | 唯一性编号  |
| Cache    | 缓存策略编号,引用自Cache标签  |
| CommandType    | Text/StoredProcedure , Default:Text |
| SourceChoice    | Unknow/Write/Read , Default:Unknow  |
| ResultMap    |  ResultMap.Id 用于结果映射，列表VS属性名映射，以及字段类型处理器处理 |
| ParameterMap    |  ParameterMap.Id |
| MultipleResultMap    | MultipleResultMap.Id，用于返回多结果集映射  |

## Statement 筛选子标签

| 标签           |    真条件   |
| :---------     | --------:|
| IsEmpty        | null or 空字符串 or 空IEnumerable |
| IsEqual        | 与比较值相等 |
| IsFalse        | 参数为 false  |
| IsGreaterEqual | 参数大于等于比较值     |
| IsGreaterThan  | 参数大于比较值        |
| IsLessEqual    | 参数小于等于比较值    |
| IsLessThan     | 参数小于比较值        |
| IsNotEmpty     | !(null or 空字符串 or 空IEnumerable) |
| IsNotEqual     | 参数不等于比较值    |
| IsNotNull      | 参数不等于 null  |
| IsNull         | 参数等于 null    |
| IsProperty     | 查询对象包含属性名 |
| IsTrue         | 参数为 true         |
| Switch         | Switch 标签      |
| Case           | Switch标签的子标签,等于比较值 |
| Defalut        | Switch标签的子标签,未命中任何Case子标签时命中此标签  |

## Statement 其他子标签

| 标签       |    说明   |
| :--------- | --------:|
| Env        | 用于判断全局变量 DbProvider |
| Include    | 引用外部Statement     |
| Dynamic    | 动态标签,用于包裹筛选标签,匹配的第一个筛选标签的前缀将忽略 |
| For        | 用于参数为IEnumerable,遍历参数动态拼接Sql |
| Where      | 继承至Dynamic,用于包裹筛选标签,匹配的第一个筛选标签前缀被忽略,并添加 Where 前缀|
| Set      | 继承至Dynamic,用于Update,包裹筛选标签,匹配的第一个筛选标签前缀被忽略,并添加 Set 前缀,必须匹配至少一个子标签，否则将抛出SmartSqlException异常。|
| Placeholder    | 占位符标签，用于替换参数键值 |

### Include 标签

| 属性       |    说明   |
| :--------- | --------:|
| RefId        | 需要引入的StatmentId |

``` xml
    <Statement Id="CommonQueryParams">
        WHERE Id = @Id
    </Statement>
    <Statement Id="Query">
        SELECT * FROM T_Test
        <Include RefId="CommonQueryParams" />
    </Statement>    
```

``` sql
    SELECT * FROM T_Test
    WHERE Id = 1

```

### For 标签

| 属性      |    说明   |
| :---------| --------:|
| Open      | 循环开始时注入的值 |
| Close     | 循环结束时注入的值 |
| Separator | 每次循环间隔时注入的值 |
| Key       | 当前循环下的对象 |


#### Example 值类型数组

``` csharp
    public class RequestParam
    {
        public IEnumerable<long> List { get; set; }
    }

    List = [1,2,3]
```

``` xml
    <Statement Id="Query">
        SELECT * FROM T_Test
        WHERE Id IN
        <For Open="(" Close=")" Key="Item" Prepend="AND" Property="List" Separator=",">
            @Item
        </For>
    </Statement>    
```

``` sql
    SELECT * FROM T_Test
    WHERE Id IN
    (1,2,3)
```

#### Example 对象数组

``` csharp
    public class T_User
    {
        public long Id { get; set; }
        public string Name { get; set; }
    }
    public class RequestParam
    {
        public IEnumerable<T_User> List { get; set; }
    }

    List = [{ Id = 1, Name = "Ahoo" },{ Id = 2, Name = "Noah" }]
```

``` xml
    <Statement Id="Query">
        INSERT INTO T_User
        (Id
        ,Name)
        VALUES
        <For Open="" Close="" Key="Item" Prepend="" Property="List" Separator=",">
            (@Id
            ,@Name)
        </For>        
    </Statement>    
```

``` sql
    INSERT INTO T_User (Id,Name)
    VALUES (1,Ahoo),(2,Noah)
```

## Cache 标签

| 属性       |    说明   |
| :--------- | --------:|
| Id    | 唯一性标号  |
| Type   | Cache类型继承自ICacheProvider,内置常量:Lru 最近最少使用算法,内存缓存, Fifo 先进先出算法,其他继承自ICacheProvider缓存类型均可,例: Type="SmartSql.Cache.Redis.RedisCacheProvider,SmartSql.Cache.Redis" |

### Cache 子标签

| 标签           |    说明   |
| :---------     | --------:|
| FlushInterval  | 定时刷新策略 |
| FlushOnExecute | 事件触发策略 |
| Parameter | 作为 ICacheProvider 初始化参数  |

### FlushInterval

| 属性       |    说明   |
| :--------- | --------:|
| Hours    | 时  |
| Minutes   | 分 |
| Seconds   | 秒 |

### FlushOnExecute

| 属性       |    说明   |
| :--------- | --------:|
| Statement    | 触发刷新缓存的声明  |

### Parameter

| 属性       |    说明   |
| :--------- | --------:|
| Key    | 键  |
| Value    | 值  |