# Asp.Net Core 最佳实践

## 常规操作
### 安装 SmartSql
``` powershell
Install-Package SmartSql
```

### 安装 SmartSql.DIExtension
``` powershell
Install-Package SmartSql.DIExtension
```
### 配置SmartSqlConfig.xml
- 写库（Write）必选 唯一节点
- 读库（Read）可选 多节点配置
#### MYSQL
- 安装mysql 客户端 *Mysql.Data*
``` powershell
Install-Package Mysql.Data
```
- 配置SmartSqlMapConfig.xml
``` xml
<?xml version="1.0" encoding="utf-8" ?>
<SmartSqlMapConfig xmlns="http://SmartSql.net/schemas/SmartSqlMapConfig.xsd">
  <Settings IsWatchConfigFile="false" />
  <Database>
    <DbProvider Name="MySqlClientFactory" ParameterPrefix="?" Type="MySql.Data.MySqlClient.MySqlClientFactory,MySql.Data"/>
    <Write Name="WriteDB" ConnectionString="Data Source=.;database=SmartSqlDB;uid=SmartSql;pwd=Rocher2018;Charset=utf8;SslMode=none"/>
    <Read Name="ReadDB-0" ConnectionString="Data Source=.;database=SmartSqlDB-Read1;uid=SmartSql;pwd=Rocher2018;Charset=utf8;SslMode=none" Weight="80"/>
    <Read Name="ReadDB-1" ConnectionString="Data Source=.;database=SmartSqlDB-Read2;uid=SmartSql;pwd=Rocher2018;Charset=utf8;SslMode=none" Weight="20"/>
  </Database>
  <SmartSqlMaps>
    <!--两种配置方式 推荐Directory模式-->
    <SmartSqlMap Path="Maps" Type="Directory"></SmartSqlMap>    
    <SmartSqlMap Path="Maps/T_User.xml" Type="File"></SmartSqlMap> 
  </SmartSqlMaps>
</SmartSqlMapConfig>
```

#### MSSQL
``` xml
<?xml version="1.0" encoding="utf-8" ?>
<SmartSqlMapConfig xmlns="http://SmartSql.net/schemas/SmartSqlMapConfig.xsd">
  <Settings
     IsWatchConfigFile="true"
  />
  <Database>
    <!--ParameterPrefix:[SqlServer:@ | MySQL:? |Oracle::] -->
    <DbProvider Name="SqlClientFactory" ParameterPrefix="@" Type="System.Data.SqlClient.SqlClientFactory,System.Data.SqlClient"/>
    <Write Name="WriteDB" ConnectionString="Data Source=.;database=SmartSqlDB;uid=SmartSql;pwd=Rocher2018"/>
    <Read Name="ReadDB-1" ConnectionString="Data Source=.;database=SmartSqlDB-Read1;uid=SmartSql;pwd=Rocher2018"/>
    <Read Name="ReadDB-2" ConnectionString="Data Source=.;database=SmartSqlDB-Read2;uid=SmartSql;pwd=Rocher2018"/>
  </Database>
  <SmartSqlMaps>
    <SmartSqlMap Path="Maps" Type="Directory"></SmartSqlMap>
  </SmartSqlMaps>
</SmartSqlMapConfig>
```
#### Postgresql
- Postgresql 客户端 *Npgsql*
``` powershell
Install-Package Npgsql
```
``` xml
<?xml version="1.0" encoding="utf-8" ?>
<SmartSqlMapConfig xmlns="http://SmartSql.net/schemas/SmartSqlMapConfig.xsd">
  <Settings
    IsWatchConfigFile="true"
  />
  <Database>
    <!--ParameterPrefix:[SqlServer:@ | MySQL:? |Oracle::|Postgresql:@] -->
    <DbProvider Name="SqlClientFactory" ParameterPrefix="@" Type="Npgsql.NpgsqlFactory,Npgsql"/>
    <Write Name="WriteDB" ConnectionString="Server=localhost;Port=5432;User Id=postgres;Password=Rocher2016; Database=SmartSqlDB;"/>
  </Database>
  <SmartSqlMaps>
    <SmartSqlMap Path="Maps/" Type="Directory"></SmartSqlMap>
  </SmartSqlMaps>
</SmartSqlMapConfig>
```

### 注入依赖

``` csharp
 services.AddSmartSql();
 services.AddRepositoryFactory();
 services.AddRepositoryFromAssembly((options) =>
 {
    options.AssemblyString = "SmartSql.Starter.Repository";
 });
```

### 定义仓储接口

``` csharp
    /// <summary>
    /// 属性可选： [SqlMap(Scope = "User")] ,不设置 则默认 Scope 模板：I{Scope}Repository
    /// 可传入自定义模板
    /// RepositoryBuilder builder=new RepositoryBuilder("I{Scope}DAL");
    /// </summary>
    public interface IUserRepository
    {
        /// <summary>
        /// 属性可选 [Statement(Execute = ExecuteBehavior.Auto,Id = "Query")]
        /// 默认 Execute：Auto ，自动判断 执行类型
        /// 默认 Id : 方法名
        /// </summary>
        /// <param name="reqParams"></param>
        /// <returns></returns>
        IEnumerable<User> Query(object reqParams);
        long GetRecord(object reqParams);
        User Get(object reqParams);
        long Insert(User entity);
        int Update(User entity);
        int Delete(User entity);
    }
```

### 尽情享用

``` csharp
    public class UserService
    {
        private readonly ISmartSqlMapper _smartSqlMapper;
        private readonly IUserRepository _userRepository;

        public UserService(
             ISmartSqlMapper smartSqlMapper
            , IUserRepository userRepository)
        {
            _smartSqlMapper = smartSqlMapper;
            _userRepository = userRepository;
        }

        public long Add(AddRequest request)
        {
            int existsNum = _userRepository.Exists(new { request.UserName });
            if (existsNum > 0)
            {
                throw new ArgumentException($"{nameof(request.UserName)} has already existed!");
            }
            return _userRepository.Add(new Entitiy.User
            {
                UserName = request.UserName,
                Password = request.Password,
                Status = Entitiy.UserStatus.Ok,
                CreationTime = DateTime.Now,
            });
        }

        public void UseTransaction()
        {
            try
            {
                _smartSqlMapper.BeginTransaction();
                //Biz();
                _smartSqlMapper.CommitTransaction();
            }
            catch (Exception ex)
            {
                _smartSqlMapper.RollbackTransaction();
                throw ex;
            }
        }
    }
```