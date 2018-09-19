# 快速使用

## ISmartSqlMapper 常用(部分)接口概述

| 函数           |    说明   |
| :---------     | --------:|
| Execute  |  IDbCommand.ExecuteNonQuery，执行返回受影响行数 |
| ExecuteScalar  | IDbCommand.ExecuteScalar,执行并返回查询返回的ReultSet中第一行的第一列  |
| Query  | 执行返回实体列表 |
| QuerySingle  | 执行返回单个实体 |
| GetDataTable  | 执行返回DataTable |
| GetDataSet  | 执行返回DataSet |
| BeginTransaction  | 开启事务 |
| CommitTransaction  | 提交事务 |
| RollbackTransaction  | 回滚事务 |

## 新增

### Statement

``` xml
    <Statement Id="Insert">
      INSERT INTO T_User
      (UserName
      ,Password
      ,Status
      ,LastLoginTime
      ,CreationTime)
      VALUES
      (?UserName
      ,?Password
      ,?Status
      ,?LastLoginTime
      ,?CreationTime)
      ;Select Last_Insert_Id();
    </Statement>
```

### 返回主键

``` csharp

            ISmartSqlMapper SqlMapper = MapperContainer.Instance.GetSqlMapper();
            long userId = _smartSqlMapper.ExecuteScalar<long>(new RequestContext
            {
                Scope = "User",
                SqlId = "Insert",
                Request = new User
                {
                    UserName = request.UserName,
                    Pwd = request.Pwd,
                    Status = Entitiy.UserStatus.Ok,
                    CreationTime = DateTime.Now,
                }
            });
```

### 新增返回受影响行数

``` csharp
            SqlMapper.Execute(new RequestContext
            {
                Scope = "User",
                SqlId = "Insert",
                Request = new User
                {
                    UserName = request.UserName,
                    Pwd = request.Pwd,
                    Status = Entitiy.UserStatus.Ok,
                    CreationTime = DateTime.Now,
                }
            });
```

## 删除

``` xml
    <Statement Id="Delete">
      Delete FROM  T_User
      Where Id=?Id
    </Statement>
```

``` csharp
            SqlMapper.Execute(new RequestContext
            {
                Scope = "User",
                SqlId = "Delete",
                Request = new { Id = 3 }
            });
```

## 更新

### Statement.Update

``` xml
<Statement Id="Update">
      UPDATE T_User
      <Set>
        <IsProperty Prepend="," Property="UserName">
          UserName = ?UserName
        </IsProperty>
        <IsProperty Prepend="," Property="Password">
          Password = ?Password
        </IsProperty>
        <IsProperty Prepend="," Property="Status">
          Status = ?Status
        </IsProperty>
        <IsProperty Prepend="," Property="LastLoginTime">
          LastLoginTime = ?LastLoginTime
        </IsProperty>
        <IsProperty Prepend="," Property="CreationTime">
          CreationTime = ?CreationTime
        </IsProperty>
      </Set>
      Where Id=?Id
    </Statement>
```

### 全量更新

``` csharp
            SqlMapper.Execute(new RequestContext
            {
                Scope = "User",
                SqlId = "Update",
                Request = new User
                {
                    Id=1,
                    UserName = request.UserName,
                    Pwd = request.Pwd,
                    Status = Entitiy.UserStatus.Ok,
                    CreationTime = DateTime.Now,
                }
            });
```

### 局部更新

``` csharp
            SqlMapper.Execute(new RequestContext
            {
                Scope = "User",
                SqlId = "Update",
                Request = new { Id=1 , Pwd = "SmartSql" }
            });
```

## 查询 返回List

``` xml
<Statement Id="Query">
      SELECT T.* From T_User T
      <Where>
        <IsNotEmpty Prepend="And" Property="EqUserName">
          T.UserName=$EqUserName
        </IsNotEmpty>
        <IsNotEmpty Prepend="And" Property="UserName">
          T.UserName Like Concat('%',$UserName,'%')
        </IsNotEmpty>
      </Where>
      <Switch Prepend="Order By" Property="OrderBy">
        <Default>
          T.Id Desc
        </Default>
      </Switch>
      <IsNotEmpty Prepend="Limit" Property="Taken">?Taken</IsNotEmpty>
    </Statement>
```

``` csharp
            var list = SqlMapper.Query<User>(new RequestContext
            {
                Scope = "User",
                SqlId = "Query",
                Request = new
                {
                    Taken = 10
                }
            });

```

## 查询 返回单个实体

``` xml
    <Statement Id="GetEntity">
      Select T.* From T_User T
      <Where>
        <IsNotEmpty Prepend="And" Property="Id">
          T.Id=?Id
        </IsNotEmpty>
      </Where>
      Limit 1
    </Statement>
```

``` csharp
            var user = SqlMapper.QuerySingle<User>(new RequestContext
            {
                Scope = "User",
                SqlId = "GetEntity",
                Request = new { Id = 1 }
            });
```

## 事务

``` csharp
            try
            {
                SqlMapper.BeginTransaction();
                //BizCode();
                SqlMapper.CommitTransaction();
            }
            catch (Exception ex)
            {
                SqlMapper.RollbackTransaction();
                throw ex;
            }
```

## 存储过程

``` csharp
            DbParameterCollection dbParameterCollection = new DbParameterCollection();
            dbParameterCollection.Add(new DbParameter
            {
                Name = "Total",
                DbType = System.Data.DbType.Int32,
                Direction = System.Data.ParameterDirection.Output
            });
            RequestContext context = new RequestContext
            {
                CommandType = System.Data.CommandType.StoredProcedure,
                RealSql = "SP_QueryByPage",
                Request = dbParameterCollection
            };
            var list = SqlMapper.Query<User>(context);
            var total = dbParameterCollection.GetValue<int>("Total");
```