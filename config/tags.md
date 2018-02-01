# SmartSqlMap 标签

## 筛选标签总览
| 标签       |    说明   | 
| :--------- | --------:| 
| Dynamic    |          |  
| Env        |          |  
| For        |          | 
| Include        |          | 
| IsEmpty        |          | 
| IsEqual        |          | 
| IsFalse        |          | 
| IsGreaterEqual        |          | 
| IsGreaterThan        |          | 
| IsLessEqual        |          | 
| IsLessThan        |          | 
| IsNotEmpty        |          | 
| IsNotEqual        |          | 
| IsNotNull        |          | 
| IsNull        |          | 
| IsProperty        |          | 
| IsTrue        |          | 
| Switch        |          | 
| Case        |          | 
| Defalut        |          | 
| Where        |          | 

### IsNotEmpty
| 属性       |    说明   | 
| :--------- | --------:| 
| Prepend    |    查询前缀      |  
| Property   |  参数键名        |  

#### Demo
``` xml
<IsNotEmpty Prepend="And" Property="OrderStatus">
    T.OrderStatus=@OrderStatus
</IsNotEmpty>
```