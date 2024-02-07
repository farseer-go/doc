# 更新记录
## 版本号说明
版本号构成：主版本号.次版本号.修订版本号

次版本号：每次新功能发布，将同步所有组件的次版本号为同一个版本号，如v0.8.0指所有组件的版本号均升级到这个版本号。

修订版本号：是针对具体的组件做单独的修订发布，这个发布只针对某个组件，而不是全部组件（不会影响到其它组件的版本号）。
## farseer-go `v0.13.0` by 2024.2.4
- `fs`
  1. 新增：Trace-Level链路层级字段
  2. 新增：parse.Convert支持json.Number类型转数字
  3. 新增：types.IsDictionaryByType、IsPageListByType 类型判断方法
  4. 新增：types.GetDictionaryToMap获取Dictionary的map元素
  5. 新增：dateTime.Parse，支持对字符串时间解析
  6. 新增：types.GetListToArrayValue函数
  7. 新增：添加链路明细的注释字段
  8. 新增：高性能的反射包fastReflect
  9. 新增：dateTime类型、Time类型转string
  10. 调整：移除调度中心TaskGroupId
  11. 调整：忽略routine v1.1.3
  12. 调整：链路追踪增加高度中心的Data参数保存
  13. 调整：添加过滤查找异常调用文件的系统组件
  14. 调整：使用新的分布式ID生成器
  15. 调整：异常日志，增加调用上游信息打印
  16. 调整：fops项目启动时，延迟3秒注册自己
  17. 调整：types.IsGoBasicType将dateTime.DateTime判断为true（基础类型）
  18. 调整：parse.ConvertValue函数，如果defValType为any，则直接返回
  19. 调整：提升代码健壮性
  20. 调整：容器初始化，将不再由模块控制
  21. 调整：parse.Convert的性能提升1倍
  22. 调整：parse.ConvertValue函数，判断defValType为nil的入参
  23. 修复：并发事务的线程安全问题
- `mapper`
  1. 重构整个组件的实现原理
- `collections`
  1. 新增：Dictionary.New() 初始化（用于反映时使用）
- `webapi`
  1. 调整：异常时记录到链路，并打印日志
  - 修复：多服务时，错误的获取Area变量
  - 修复：接收到int64数字丢失精度的问题
  - 优化部份实现
- `linkTrace`
  1. 新增：添加链路明细的注释字段
  - 调整：链路追踪增加调度中心的Data参数保存
  - 调整：控制3秒执行一次上传链路记录到FOPS中心
  - 调整：结束当前链路时，要清空AsyncLocal
- `data`
  1. 新增：IMigratorIndex接口，支持更易阅读的索引创建方式
  - 新增：支持唯一索引
  - 调整：移除缓存执行效率记录代码
  - 调整：禁用mitchellh/osext
  - 修复：并发事务的线程安全问题
- `fSchedule`
  1. 新增：JOB执行异常时，增加ERROR日志
  - 新增：定时10分钟，注册一次任务，防止掉线
  - 调整：移除调度中心TaskGroupId
  - 调整：注册任务时，增加corn格式验证
  - 调整：系统启动后新添加的任务，通过通知的方式，调用注册
  - 调整：链路追踪增加调度中心的Data参数保存
  - 调整：日志方法
  - 调整：启动时没有任务，跳过健康检查
  - 调整：取消休眠等待的手动埋点
  - 调整：设置队列数量、工作数量
  - 调整：修改日志内容
  - 修复：调度中心客户端组件的WebApi服务与应用的WebApi服务冲突
- `eventBus`
  1. 新增：事件驱动和链路追踪
  - 调整：使用新的分布式ID生成器
  - 调整：区分Redis订阅和本地事件订阅
- `fsctl`
  1. 新增：支持一个API多路由
- `cache`、`cacheMemory`、`elasticSearch`、`etcd`、`rabbit`、`redis`、`task`、`utils`、`queue`
  1. 仅升级依赖组件版本号
  
## farseer-go `v0.12.0` by 2024.1.9
- `fs`
  - 新增：http请求时记录出入参、状态头、header
  - 新增：调度中心使用的链路入口
  - 新增：dateTime.NewUnixMicro()方法
  - 新增：container.IsRegisterType()方法，判断类型是否注册过
  - 新增：types.IsNil 判断值是否为nil
  - 新增：ThrowRefuseExceptionBool、ThrowRefuseExceptionfBool、ThrowRefuseExceptionError 便捷方法
  - 新增：parse.Convert，支持切片转切片
  - 调整：系统启动时增加FopsServer地址打印
  - 调整：链路追踪的字段名称（入库中字段名称）
  - 调整：链路追踪改为上传模式
  - 调整：数据库Scan对time.Time类型解析
  - 调整：AppId、AppName、AppIp 迁移到core包
  - 调整：链路追踪添加明细的应用信息
  - 调整：链路追踪TraceId将int64字段转为string类型
  - 修复：链路追踪数据库名称无法获取的问题
- `mapper`
  - 新增：支持 嵌入字段
  - 新增：支持 内嵌时间转换
  - 新增：Map转结构体
  - 新增：实现指针、非指针互转
  - 新增：mapper:ignore标记，支持忽略字段的赋值
  - 修复：获取nil指针报错
- `collections`
  - 新增：collections.NewListFromChan(c) 创建集合，将chan中的数据填充到集合
  - 新增：ContainsPrefix、ContainsSuffix、ContainsAny
  - 修复：使用routine.Go，解决无法链路追踪的问题
  - 修复：修复lock为nil时，导致异常
- `webapi`
  - 调整：增加注入时，对类型检查是否注册过IOC
  - 新增：健康检查接口：webapi.UseHealthCheck()
  - 优化：减少重复赋值
- `linkTrace`
  - 新增：http请求时记录出入参、状态头、header
  - 新增：Grpc的明细
  - 调整：链路追踪的"Trace-Id"、"Trace-App-Name"
  - 调整：Error方法，当err为nil时不操作。
  - 调整：链路追踪的表字段结构
  - 调整：TraceContextPO.List改用[]any类型
  - 调整：注册数据库上下文时，提前检查连接字符串
  - 调整：链路追踪改为上传模式
  - 调整：TraceId、AppId改为string类型
- `data`
  - 新增：丰富TableSet.WhereXX相关方法（leil贡献）
  - 新增：OrderIf、DescIf、DescIfElse、AscIf、AscIfElse排序方法
  - 修复：链路追踪数据库名称无法获取的问题
  - 修复：在clickhouse数据库中，gorm官方包会出现异常：当batchSize小于lst.Count时。会收到：code: 101的错误
- `queue`
  - 调整：队列消费异常时，不修改队列位置
- `fSchedule`
  - 新增：向调度中心注册时，增加链路追踪
  - 修复：是否已注册的判断逻辑
- `cache`、`cacheMemory`、`elasticSearch`、`etcd`、`eventBus`、`fsctl`、`rabbit`、`redis`、`task`、`utils`
  1. 仅升级依赖组件版本号
  
## farseer-go `v0.11.0` by 2023.12.25
- `fs`
  1. 修复：任意数字转枚举的问题
  2. 新增：dateTime.Before方法，判断是否比dt时间小（早）
  3. 调整：框架启动完后执行的函数改为：未初始化完时，加入到列表中，否则立即执行
  4. 调整：将获取系统资源的功能移到utils组件，以减少fs对第三方依赖的依赖
- `utils`
  1. 调整：将获取系统资源的功能移到utils组件，以减少fs对第三方依赖的依赖
- `webapi`
  1. 调整：移除fasthttp组件依赖
  2. 调整：减少第三方组件依赖
  3. 新增：将Session作为子模块
- `linkTrace`
  1. 新增：链路追踪记录写入到clickhouse、elasticsearch
  2. 新增：链路追踪执行明细记录写入到clickhouse、elasticsearch
  3. 添加clickhouse驱动，实现消费子模块
  4. 添加elasticsearch驱动，实现消费子模块
- `data`
  1. 新增：WhereIf方法，当conditional==true时，使用条件
  2. 调整：将除了mysql驱动外的数据库驱动改到子模块中
  3. 修复：事务的error判断错误，导致无法提交
- `rabbit`
  1. 调整：错误日志的打印逻辑
  2. 修复：关闭时，需要判断是否为nil
- `fSchedule`
  1. 调整：ClientName改为core.AppName（原来是hostName)
  2. 调整：当向调度中心注册之后继续添加任务时，立即重新注册新的任务
  3. 调整：将获取系统资源的功能移到utils组件，以减少fs对第三方依赖的依赖
- `cache`、`cacheMemory`、`collections`、`elasticSearch`、`etcd`、`eventBus`、`fsctl`、`mapper`、`queue`、`redis`、`task`
  1. 仅升级依赖组件版本号

## farseer-go `v0.10.0` by 2023.12.18
- `fs`
  1. 新增：通过环境变量fsenv，支持多环境配置文件
  2. 新增：GetPageList获取collections.PageList的元素
  3. 调整：dateTime.DateTime类型支持gorm框架
  4. 调整：apiResponse的ServerAt字段改用dateTime类型
  5. 调整：dateTime类型，新增部份time.Time类似的方法
  6. 修复：无法将自定义枚举转数字的问题
  7. 修复：类型转换问题（枚举转数字）
  8. 修复：dateTime.DateTime类型的json转换
- `collections`
  1. 移除：MapToList、MapToArray、MapToPageList方法
- `mapper`
  1. mapper 支持 时间 转 string
  2. 调整：ToPageList方法，移除recordCount入参
  3. 修复：time.Time类型无法interface时，忽略掉
- `utils`
  1. 调整：http忽略https检查
- `redis`
  1. 调整：移除了MapToArray方法
- `webapi`
  1. 调整：增加404错误日志
- `cache`、`cacheMemory`、`elasticSearch`、`etcd`、`eventBus`、`fSchedule`、`queue`、`rabbit`、`task`
  1. 仅升级依赖组件版本号
- `fsctl`
  1. 新增：路由生成后的总数量打印
  2. 修改模板
- `linkTrace`
  1. 修复：父级明细ID读取错误
- `data`
  1. 修改错误日志
  2. 调整：移除了MapToArray方法
  3. 修复：事务中发生error错误，没有自动回滚
  4. 修复：Exprs方法无法更新多字段的问题
  5. 修复：ExecuteSqlToValue无法获取值的问题

## farseer-go `v0.9.0` by 2023.11.22
- `fs`
  1. 新增：不同操作系统的差异符号
  2. 新增：parse.Convert转换枚举类型
  3. 新增：链路追踪接口
  4. 新增：asyncLocal类型，用于实现routine的泛型版本
  5. 新增：exception.ThrowWebExceptionBool 为true时抛异常方法
  6. 新增：exception.ThrowWebExceptionError抛出WebException异常
  7. 新增：string转collections.List
  8. 新增：parse类型转换：string转time.Time、DateTime、ToInt、ToInt64、ToUInt、ToFloat32、ToFloat64、ToString、ToBool
  9. 新增：日志的时间格式配置
  10. 调整：未配置日志滚动间隔时，默认按天存储
  11. 调整：获取IP方式改为，从C类到A类的顺序获取
  12. 调整：container.ResolveType解析实例方法，增加error返回参数
  13. 调整：exception.CatchException方法内部不再捕获异常
  14. 调整：退回1.20新增的方法，降低到1.9版本的方法
  15. 修复：日志json输出、或文件写入时，没有移除颜色标记
  16. 修复：随机字符串，长度无效
  17. 修复：如果配置文件没有设置日志级别，则默认为Trace级别
  18. 修复：时间类型转换缺时区的问题
- `linkTrace`
  1. 支持：webapi组件接入链路追踪
  2. 支持：数据库的链路追踪
  3. 支持：Redis链路追踪
  4. 支持：es的链路追踪
  5. 支持：etcd链路追踪
  6. 支持：rabbit.send链路追踪
  7. 支持：rabbit的消费链路追踪
  8. 支持：queue的消费链路追踪
  9. 支持：task的消费链路追踪
  10. 支持：fschedule的消费链路追踪
  11. 支持：链路的scope范围功能
- `collections`
  1. 新增：Parallel for range 并行操作
  2. 新增：For方法，等同于for i
  3. 新增：ToList 将arr按,号分隔，转换成List[T]
  4. 新增：list.Reverse()反转集合
  5. 新增：ReadonlyDictionary.ToDictionary()转Dictionary方法
  6. 新增：AddList、AddArray方法
  7. 新增：GroupBy方法支持map[x]collections.List[x]类型
  8. 调整：将动态反射的代码实现移到fs组件中
  9. 调整：如果Enumerable对象为空，则返回默认Enumerable
  10. 调整：NewReadonlyDictionaryFromMap字典map为nil时，重新初始化
  11. 调整：types.ListAdd的参数为非指针类型
  12. 修复：没数据时使用Insert插入报错
  13. 修复：字符串排序的问题
- `mapper`
  1. 新增：mapper组件支持转换对象执行初始化方法MapperInit
  2. 新增：mapper组件 数组嵌套转换支持
  3. 新增：mapper 支持 map 非指针数组
  4. 新增：mapper 支持实体内list集合转换
  5. 新增：自定义collections.List类型转换
  6. 新增：支持 ListType 类型转换
  7. 新增：支持 decimal.Decimal
  8. 调整：mapper组件 非导出字段，接口类型的 过滤转换
- `cache`
  1. 仅升级依赖组件版本号
- `cacheMemory`
  1. 调整：异常信息提示
- `data`
  1. 新增：上下文初始化失败时提醒
  2. 新增：通用仓储方法：Update，数据修改方法
  3. 新增：通用仓储方法：IsExists，记录是否存在
  4. 新增：通用仓储方法：Delete，删除数据
  5. 新增：IMigratorCreate接口，在PO实体，可自定义创建表时的SQL
  6. 新增：获取decimal.Decimal类型字段
  7. 新增：Exprs方法，批量Expr操作数据库
  8. 新增：对Clickhouse数据库的支持
  9. 新增：Clickhouse的OptimizeFinal方法
  10. 新增：Omit方法，Update时可以忽略更新字段
  11. 新增：数据库的链路追踪
  12. 新增：实现数据库的执行耗时统计
  13. 新增：支持表引擎设置（如innodb、memory)
  14. 新增：IInternalContext接口增加：ExecuteSql、ExecuteSqlToResult、ExecuteSqlToValue方法
  15. 新增：手动注册内部上下文，以实现动态数据库方案
  16. 调整：TableSet部份方法的返回值（增加返回行数、error）
  17. 调整：健康检查时，返回数据库时间
  18. 调整：自动创建表开关改到上下文的tag中
  19. 调整：初始化tableSet时，需要优先设置tableName
  20. 调整：移除routine依赖，改用asyncLocal类型
  21. 调整：Update方法转map时，json需要单独处理
  22. 修复：Row取出为nil时，返回默认值
  23. 修复：主键字段没有做蛇形转换的问题
  24. 修复：多个上下文共享事务冲突的问题
  25. 修复：gorm的save方法在没有主键时，会以insert方式执行，与框架的用途冲突。取消insert执行
- `utils`
  1. 新增：二元操作逻辑 condition.IsTrue
  2. 新增：数学计算工具 operator.GetSum GetTail GetHead
  3. 新增：str.PadLeft 向左填充字符串方法
  4. 新增：http.GetDomain()从URL中获取Domain部份
  5. 调整：增加http状态码判断
  6. 调整：读取文件时，使用不同操作系统的差异符号
- `webapi`
  1. 新增：webapi组件接入链路追踪
  2. 新增：字段验证器
  3. 新增：webapi.UseApiDoc()开启api doc文档
  4. 新增：HttpData对象，在上下文中可以设置、获取值
  5. 新增：允许设置StatusMessage
  6. 新增：IFilter 路由过滤器接口
  7. 新增：jwt过滤器
  8. 新增：获取真实IP
  9. 新增：HttpResponse.Reject() 拒绝服务
  10. 新增：webapi.PrintRoute打印所有路由信息到控制台
  11. 新增：action.ImageResult 处理图片
  12. 调整：Mvc模式的HttpContext改为嵌入类型
  13. 调整：简化HttpResponse对象
  14. 调整：容器注入实例找不到时，抛出异常
  15. 调整：将解析request.Body、请求参数的解析放到routing中间件调用
  16. 调整：HttpResponse.Body改为[]any类型，不使用[]reflect.Value
  17. 调整：移除routine依赖，改用asyncLocal类型
  18. 调整：使用Cors组件时，插入到中间件第一个
  19. 修复：表单提交匹配dto json tag大小写问题
- `redis`
  1. 新增：Redis链路追踪
  2. 新增：redis.Register手动注册内部上下文，以实现动态Redis方案
  3. 新增：事务、管理方法
  4. 调整：健康检查时，返回Redis服务端时间
  5. 调整：移除routine依赖，改用asyncLocal类型
  6. 调整：移除redis.Nil的错误类型
  7. 修复：异常时不能写入redis
- `fSchedule`
  1. 新增：依赖链路追踪组件
  2. 新增：对接收的任务做基础检查
  3. 新增：Ver版本字段
  4. 新增：注册客户端时，新增数据上传
  5. 新增：本地调试模式
  6. 新增：TaskGroupId主键
  7. 新增：收到调度中心检查存活时打印日志
  8. 调整：日志上传改为全局批量上传模式
  9. 调整：精简代码，去除默认值的赋值。
  10. 调整：服务端注册改到健康检查中执行
  11. 调整：客户端注册时默认不发送当前IP，由服务端获取
  12. 调整：获取IP方式改为，从C类到A类的顺序获取
  13. 调整：移除routine依赖，改用asyncLocal类型
  14. 修复：错误的时间戳格式
- `elasticSearch`
  1. 新增：健康检查
  2. 新增：支持范围查询
  3. 新增：es的链路追踪
- `queue`
  1. 新增：queue的消费链路追踪
  2. 调整：移除routine依赖，改用asyncLocal类型
  3. 修复：拉取数量为0时，跳出消费
- `rabbit`
  1. 新增：自动重连机制
  2. 新增：对消费处理函数的异常捕获
  3. 新增：健康检查
  4. 新增：rabbit的消费链路追踪入口
  5. 新增：rabbit.send链路追踪
  6. 新增：消费的中，增加QueueName字段
  7. 调整：移除routine依赖，改用asyncLocal类型
  8. 修复：调试状态下无法重连的问题
- `tasks`
  1. 新增：task的消费链路追踪
  2. 调整：移除routine依赖，改用asyncLocal类型
- `etcd`
  1. 新增：etcd的链路追踪
  2. 调整：移除routine依赖，改用asyncLocal类型
- `eventBus`
  1. 新增：etcd的链路追踪
  2. 调整：移除routine依赖，改用asyncLocal类型



## farseer-go `v0.8.0` by 2023.07.27
- `fs`
  1. 调整：FarseerModule接口拆分成FarseerPreInitializeModule、FarseerInitializeModule、FarseerPostInitializeModule、FarseerShutdownModule
  2. 调整：框架启动时的日志内容
  3. 修复：FarseerKernelModule模块需要去重
  4. 修复：时间轮的时间格超出下标的错误
  5. 新增：数据库事务接口`core.ITransaction`
  6. 新增：日志接口`core.ILog`
  7. 调整：重写日志组件
  8. 调整：执行配置文件初始化逻辑改到FarseerKernelModule模块中
  9. 新增：日志打印json格式支持
  10. 新增：flog.ErrorIfExists方法
  11. 新增：container支持移除已注册的实例`container.Remove`、`container.RemoveUnused`
  12. 新增：获取系统环境资源`system.GetResource`
  13. 新增：日志写入到文件功能
- `collections`
  1. 新增：Enumerable类型的Foreach方法，快速实现遍历元素
- `mapper`
  1. 调整：部分类型转换，取消强制指针入参类型
- `cache`
  1. 调整：获取数量时，如果数量为0，添加判断是否存在KEY，KEY不存在时，重新读取数据源
  2. 调整：缓存设置过期时间的方式`Option func(*Op)`
- `cacheMemory`
  1. 修改：TTL时间入参改为cache.Option参数传入
  2. 新增：缓存时间策略
- `data`
  1. 新增：WhereIgnoreLessZero方法，自动忽略小于等于0的sql条件
  2. 新增：WhereIgnoreNil，自动忽略nil条件
  3. 调整：使用域的方式创建session，支持并发安全
  4. 新增：执行原生SQL
  5. 新增：返回原生的对象
  6. 新增：Expr对字段做表达式操作方法
  7. 新增：数据库事务功能
  8. 调整：重命名内部上下文名称为：InternalDbContext
  9. 新增：通用的仓储接口及实现
  10. 新增：DomainSet类型，实现通用CRUD绑定
  11. 新增：IInternalContext接口，可以数据库上下文直接操作原生ORM
  12. 调整：open数据库发生错误时的日志记录
- `utils`
  1. 调整：重写追加文件内容方法
- `webapi`
  1. 调整：json输出时，Content-Type设置为application/json
  2. 新增：支持action入参的接口类型IocName设置
  3. 新增：Cookies功能
  4. 新增：Session功能（支持本地存储、Redis存储）
  5. 新增：minimalApi支持获取HttpContext
  6. 调整：重写路由机制，自定义实现serveMux
  7. 新增：实现正则路由匹配（占位符变量）
  8. 修复：multipart/form-data取不到值的BUG
  9. 修复：视图路径查找缺失PATH的问题
- `fSchedule`
  1. 新增：客户端环境资源收集
- `redis`
  1. 新增：HashIncrXXX,Hash对field加减
  2. 修改：TTL时间入参改为cache.Option参数传入
  3. 新增：缓存时间策略

## farseer-go `v0.7.0` by 2023.03.03
- `fs`
  1. 新增：IsDtoModelIgnoreInterface函数
  2. 新增：`事件通用接口`
  3. 新增：`container.ResolveIns`将现有中实例内的字段做注入操作
  4. 新增：`日志等级判断`
  5. 新增：增加毫秒打印
  6. 新增：`时间轮算法`
  7. 新增：控制台颜色打印输出
  8. 新增：`GetSubNodes`增加对环境变量的支持
  9. 新增：`ResolveAll`从容器中获取所有实例
  10. 新增：`健康检查`，不通过则直接退出应用
  11. 调整：flog.panic，err非nil时才执行
  12. 调整：配置设置`默认值`时，`增加GetSubNodes读取`
  13. 调整：将事件参数移到fs模块
  14. 调整：订阅函数统一到fs模块
  15. 调整：打印日志，取消使用chan
  16. 调整：框架启动回调，增加标题
  17. 调整：error日志增加源文件、行号输出
  18. 调整：日志输出格式
  19. 修复：types.GetRealType获了取真实类型时，需判断值是否为nil
  20. 修复：容器增加读写锁，解决并发读写问题
- `collections`
  1. 新增：List集合增加`AddRange`方法
  2. 新增：`ReadonlyDictionary`对象
  3. 调整：序列化实现
  4. 修复：json反序列时，lock为nil的问题
  5. 修复：判断是否为空时，同时判断锁是否初始化过
- `mapper`
  1. 优化：优化 auto 方法
  2. 调整：支持新版本的collections v0.4.0转换
- `cache`
  1. 增加：设置定义将`缓存定时同步`
  2. 调整：抽象出`ICacheManager`接口，并注册到容器中
  3. 调整：重新设计缓存模块
  4. 调整：注册缓存时，增加ICacheManage返回值
  5. 调整：修改cacheId为any类型
- `cacheMemory`
  1. 调整：抽象出`ICacheManager`接口，并注册到容器中
  2. 调整：增加Lock锁
  3. 调整：重新设计缓存模块
  4. 调整：SetProfiles函数返回cache.ICacheManage[TEntity]类型
  5. 调整：EnableItemNullToLoadALl重命名为EnableItemNullToLoadAll
  6. 调整：time.After方式，避免内存泄露
- `redis`
  1. 新增：`订阅功能`
  2. 新增：`RegisterEvent`函数，可以通过`core.IEvent`接口获取
  3. 新增：获取原生的客户端
  4. 新增：`Redis健康检查`
  5. 新增：`选举功能`
  6. 调整：cache缓存
  7. 调整：SetProfiles增加增加ICacheManage返回值
  8. 调整：configure.ParseConfig -> configure.ParseString函数名称
  9. 调整：ToEntity增加exists返回值
  10. 调整：Redis使用方式，改为redis.IClient接口方式使用
  11. 调整：time.After方式，避免内存泄露
  12. 调整：修改cacheId为any类型
  13. 调整：统一使用fs.Context
- `data`
  1. 新增：`UpdateOrInsert`方法
  2. 新增：`批量写入`
  3. 新增：`数据库健康检查`
  4. 新增：`自动创建表功能`
  5. 调整：configure.ParseConfig -> configure.ParseString函数名称
  6. 调整：time.After方式，避免内存泄露
  7. 调整：异常时，不使用panic
  8. 调整：数据找不到时，不提示
  9. 调整：日志输出格式
  10. 调整：空闲连接数为最大连接的1/3
  11. 调整：重写数据库open逻辑
- `eventBus`
  1. 新增：`RegisterEvent`函数，可以通过`core.IEvent`接口获取
  2. 调整：PublishEvent、PublishEventAsync 增加error返回
  3. 调整：增加try以避免事件函数出现panic异常。
  4. 调整：事件注册到core.IEvent接口，与redis共用一个接口。
- `queue`
  1. 新增：读写锁防止并发读写问题
- `tasks`
  1. 优化代码
- `utils`
  1. 调整：httpRequest增加statusCode返回
  2. 调整：httpRequest取消打印错误消息
  3. 调整：httpRequest取消重试
  4. 调整：httpRequest自动转换[]byte
  5. 调整：修改http错误时的说明文字
  6. 调整：exec/shell ctx 默认第一个参数
  7. 修复：httpRequest x-www-form-urlencoded错误的body组装
  8. 修复：有可能导致panic
- `webapi`
  1. 新增：增加`https`的支持
  2. 新增：`DTO`模式下的json Tag`自定义字段名称`
  3. 新增：对外使用http包的Request和Response
  4. 新增：minimalApi入参为`dto模式`时，`支持interface注入`
  5. 新增：`开启pprof功能`
  6. 新增：`实现多开实例`
  7. 新增：`实现API函数自动容器注入`
  8. 调整：invoke接口用时
  9. 调整：构造Action入的函数名为BuildActionInValue
  10. 调整：注册失败时os.exit改为panic
  11. 调整：Run函数取地址不再判断第一个参数是否为空
  12. 调整：改为使用自定义ServeMux对象
  13. 调整：移除init方法
  14. 调整：httpContext.Header改为只读字典类型
  15. 调整：BodyString赋值，交由callResult处理
  16. 调整：Method支持多个类型，用|分隔，如（POST|GET|PUT|DELETE）
  17. 调整：修改HttpRequest字段命名
  18. 优化：优化中间件初始化过程，大幅提升每次API请求的性能。
  19. 优化：提前确定基础类型，减少运行时判断
  20. 修复：异常时，无法转换exp为string的错误
  21. 修复：routing中间件未过滤OPTIONS引起CORS失败的问题
- `rabbit`
  1. 调整：time.After方式，避免内存泄露
  2. 修复：日志格式错误

## farseer-go `v0.6.0` by 2023.01.11
- `fs`
  1. 新增：core.NewApiResponseByReader方法
  2. 新增：parse.Convert`增加time.Time与dateTime.DateTime类型互转`
  3. 新增：configure.ParseConfig增加`配置文件转实体结构的方法`
  4. 新增：flog.Panic函数
  5. 调整：配置读取GetXXX的方式
  6. 调整：flog.Error改为error返回类型
  7. 优化：优化代码
  8. 优化：优化字段赋值，使用parse.ConvertValue方式
  9. 修复：parse.Convert转换时，需要对source判断nil
  10. 修复：reflect.Type类型不需要判断reflect.Interface

## farseer-go `v0.5.0` by 2022.12.28
- `fs`
  1. 新增：`container.ResolveType` 根据接口类型查找实现
  2. 调整：container增加判断接口类型是否注册过
  3. 调整：所有提示，改为英文
  4. 优化：优化代码
  5. 修复：要注入的接口，需先判断是否为nil，只有nil才注入
- `collections`
  1. 调整：增加读写锁
  2. 优化：优化代码

## farseer-go `v0.4.0` by 2022.12.20
- `fs`
  1. 新增：parse.Convert中`bool转string`
  2. 调整：使用自研的配置组件（移除第三方组件）
  3. 调整：使用自研的container组件（移除第三方组件）
  4. 调整：移除go.work
  5. 修复：types.IsGoBasicType方法的BUG
- `collections`
  1. 调整：移除go.work
- `mapper`
  1. 修复：time.Time类型转换
- `data`
  1. 调整：移除go.work
  2. 调整：升级依赖
- `tasks`
  1. 调整：移除go.work
  2. 调整：升级依赖

## farseer-go `v0.3.0` by 2022.12.12
- `fs`
  1. 新增：types.IsGoBasicType 是否为Go内置基础类型
  2. 新增：reflect.Type的类型转换（parse.ConvertValue）
  3. 调整：支持parse.Convert支持string转string[]
- `collections`
  1. 新增：支持数据库varchar类型，按,号分割转换成list
  2. 修复：json序列化问题
- `webapi`
  1. 新增：`header自动绑定`
  2. 新增：`IActionFilter过滤器`
  3. 新增：`批量注册路由`方法`webapi.RegisterRoutes`
  4. 调整：重命名handleMiddleware
  5. 调整：mvc控制器，路由名称转为小写
  6. 调整：mvc控制器，增加对Action的参数设置
  7. 修复：Response输出报文时，非Go基础类型，均转成json
  8. 修复：controller.BaseController初始化时的错误
  9. 修复：异常内容转成string的错误

## farseer-go `v0.2.0` by 2022.12.06
- `fs`
  1. 新增：types增加判断是否为内置集合类型
  2. 新增：types增加获取函数的出参、入参
  3. 新增：types增加判断是否为DTO参数
  4. 新增：WebException异常
  5. 新增：ApiResponse增加转Json、转Bytes方法
  6. 新增：增加模块依赖检查功能
  7. 修复：json序列化问题
- `mapper`
  1. 调整：ToPageList支持：ListAny、List[xx]、[]xx转List[yy]
- `utils`
  1. 调整：http.client改为链式调用

## farseer-go `v0.1.0` by 2022.09.20

    我们非常高兴地发布第一个外部版本号v0.1.0
    在此之前，我们的团队一直在努力为框架添加各种功能，以满足业务系统的需求。
    3个月后，我们将常用的功能添加到框架中，并通过初步测试验证。
    因此，我们的团队决定发布这个版本号。 
    需要注意的是，在v1到来之前，v0，仍然会有很多小问题。但是，我们会努力迅速修复发现的问题。
    如果你在使用过程中发现任何bug，也请发问题或PR，谢谢。

