# Asp.Net Core 最佳实践

## 安装 SmartSql.DIExtension

``` chsarp
Install-Package SmartSql.DIExtension
```

## 注入依赖

``` csharp
 services.AddSmartSql();
 services.AddRepositoryFactory();
 services.AddRepositoryFromAssembly((options) =>
 {
    options.AssemblyString = "SmartSql.Starter.Repository";
 });
```

## 定义仓储接口

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
        int Delete(User enttiy);
    }
```

## 尽情享用

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