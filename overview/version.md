# 更新记录

## farseer-go`v0.7.0` by 2023.03.03
```go
fs              v0.6.0 -> v0.7.0 / 2023.03.03
collections     v0.3.0 -> v0.4.0 / 2023.03.03
mapper          v0.2.0 -> v0.3.0 / 2023.03.06
cache           v0.1.1 -> v0.2.0 / 2023.03.03
cacheMemory     v0.1.0 -> v0.2.0 / 2023.03.03
redis           v0.1.0 -> v0.2.0 / 2023.03.03
data            v0.1.1 -> v0.2.0 / 2023.03.03
eventBus        v0.1.0 -> v0.2.0 / 2023.03.03
queue           v0.1.0 -> v0.2.0 / 2023.03.03
tasks           v0.1.1 -> v0.2.0 / 2023.03.03
utils           v0.2.0 -> v0.3.0 / 2023.03.03
webapi          v0.2.0 -> v0.3.0 / 2023.03.03
etcd            v0.1.0 -> 
rabbit          v0.1.0 -> v0.2.0 / 2023.03.03
fShedule        v0.0.0 -> v0.1.0 / 2023.03.03
async           v0.1.0-alpha ->
elasticSearch   v0.1.0-alpha -> 
```
`fs v0.7.0` / 2023.03.03

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

`collections v0.4.0` / 2023.03.03

1. 新增：List集合增加`AddRange`方法
2. 新增：`ReadonlyDictionary`对象
3. 调整：序列化实现
4. 修复：json反序列时，lock为nil的问题
5. 修复：判断是否为空时，同时判断锁是否初始化过

`mapper v0.3.0` / 2023.03.06

1. 优化：优化 auto 方法
2. 调整：支持新版本的collections v0.4.0转换

`cache v0.2.0` / 2023.03.03

1. 增加：设置定义将`缓存定时同步`
2. 调整：抽象出`ICacheManager`接口，并注册到容器中
3. 调整：重新设计缓存模块
4. 调整：注册缓存时，增加ICacheManage返回值
5. 调整：修改cacheId为any类型

`cacheMemory v0.2.0` / 2023.03.03

1. 调整：抽象出`ICacheManager`接口，并注册到容器中
2. 调整：增加Lock锁
3. 调整：重新设计缓存模块
4. 调整：SetProfiles函数返回cache.ICacheManage[TEntity]类型
5. 调整：EnableItemNullToLoadALl重命名为EnableItemNullToLoadAll
6. 调整：time.After方式，避免内存泄露

`redis v0.2.0` / 2023.03.03

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

`data v0.2.0` / 2023.03.03

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

`eventBus v0.2.0` / 2023.03.03

1. 新增：`RegisterEvent`函数，可以通过`core.IEvent`接口获取
2. 调整：PublishEvent、PublishEventAsync 增加error返回
3. 调整：增加try以避免事件函数出现panic异常。
4. 调整：事件注册到core.IEvent接口，与redis共用一个接口。

`queue v0.2.0` / 2023.03.03

1. 新增：读写锁防止并发读写问题

`tasks v0.2.0` / 2023.03.03

1. 优化代码

`utils v0.3.0` / 2023.03.03

1. 调整：httpRequest增加statusCode返回
2. 调整：httpRequest取消打印错误消息
3. 调整：httpRequest取消重试
4. 调整：httpRequest自动转换[]byte
5. 调整：修改http错误时的说明文字
6. 调整：exec/shell ctx 默认第一个参数
7. 修复：httpRequest x-www-form-urlencoded错误的body组装
8. 修复：有可能导致panic

`webapi v0.3.0` / 2023.03.03

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

`rabbit v0.2.0` / 2023.03.03

1. 调整：time.After方式，避免内存泄露
2. 修复：日志格式错误

## farseer-go`v0.6.0` by 2023.01.11
```go
fs              v0.5.0 -> v0.6.0 / 2023.01.11
collections     v0.3.0 -> 
mapper          v0.2.0 ->
cache           v0.1.1 ->
cacheMemory     v0.1.0 ->
redis           v0.1.0 ->
data            v0.1.1 -> 
eventBus        v0.1.0 ->
queue           v0.1.0 ->
tasks           v0.1.1 -> 
utils           v0.2.0 ->
webapi          v0.2.0 -> 
etcd            v0.0.0 -> v0.1.0 / 2023.01.21
rabbit          v0.0.0 -> v0.1.0 / 2023.01.13
fShedule        v0.0.0 ->
async           v0.0.0 -> v0.1.0-alpha / 2023.01.03
elasticSearch   v0.0.0 -> v0.1.0-alpha / 2023.01.03
```
`fs v0.6.0` / 2023.01.11

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

## farseer-go`v0.5.0` by 2022.12.28
```go
fs              v0.4.1 -> v0.5.0 / 2022.12.28
collections     v0.2.1 -> v0.3.0 / 2022.12.31
mapper          v0.2.0 ->
cache           v0.1.1 ->
cacheMemory     v0.1.0 ->
redis           v0.1.0 ->
data            v0.1.1 -> 
eventBus        v0.1.0 ->
queue           v0.1.0 ->
tasks           v0.1.1 -> 
utils           v0.2.0 ->
webapi          v0.2.0 -> 
etcd            v0.0.0 ->
rabbit          v0.0.0 ->
fShedule        v0.0.0 ->
async           v0.0.0 ->
elasticSearch   v0.0.0 ->
```
`fs v0.5.0` / 2022.12.28

1. 新增：`container.ResolveType` 根据接口类型查找实现
2. 调整：container增加判断接口类型是否注册过
3. 调整：所有提示，改为英文
4. 优化：优化代码
5. 修复：要注入的接口，需先判断是否为nil，只有nil才注入

`collections v0.3.0` / 2022.12.31

1. 调整：增加读写锁
2. 优化：优化代码

## farseer-go`v0.4.0` by 2022.12.20
```go
fs              v0.3.0 -> v0.4.0 / 2022.12.18 -> v0.4.1 / 2022.12.20
collections     v0.2.0 -> v0.2.1 / 2022.12.20
mapper          v0.1.1 -> v0.2.0 / 2022.12.20
cache           v0.1.1 ->
cacheMemory     v0.1.0 ->
redis           v0.1.0 ->
data            v0.1.0 -> v0.1.1 / 2022.12.20
eventBus        v0.1.0 ->
queue           v0.1.0 ->
tasks           v0.1.0 -> v0.1.1 / 2022.12.20
utils           v0.2.0 ->
webapi          v0.2.0 -> 
etcd            v0.0.0 ->
rabbit          v0.0.0 ->
fShedule        v0.0.0 ->
async           v0.0.0 ->
elasticSearch   v0.0.0 ->
```
`fs v0.4.1` / 2022.12.20

1. 新增：parse.Convert中`bool转string`
2. 调整：使用自研的配置组件（移除第三方组件）
3. 调整：使用自研的container组件（移除第三方组件）
4. 调整：移除go.work
5. 修复：types.IsGoBasicType方法的BUG

`collections v0.2.1` / 2022.12.20

1. 调整：移除go.work

`mapper v0.2.0` / 2022.12.20

1. 修复：time.Time类型转换

`data v0.1.1` / 2022.12.20

1. 调整：移除go.work
2. 调整：升级依赖

`tasks v0.1.1` / 2022.12.20

1. 调整：移除go.work
2. 调整：升级依赖

## farseer-go`v0.3.0` by 2022.12.12
```go
fs              v0.2.0 -> v0.3.0 / 2022.12.11
collections     v0.1.0 -> v0.2.0 / 2022.12.12
mapper          v0.1.1 ->
cache           v0.1.1 ->
cacheMemory     v0.1.0 ->
redis           v0.1.0 ->
data            v0.1.0 ->
eventBus        v0.1.0 ->
queue           v0.1.0 ->
tasks           v0.1.0 ->
utils           v0.2.0 ->
webapi          v0.1.0 -> v0.2.0 / 2022.12.12
etcd            v0.0.0 ->
rabbit          v0.0.0 ->
fShedule        v0.0.0 ->
async           v0.0.0 ->
elasticSearch   v0.0.0 ->
```

`fs v0.3.0` / 2022.12.11

1. 新增：types.IsGoBasicType 是否为Go内置基础类型
2. 新增：reflect.Type的类型转换（parse.ConvertValue）
3. 调整：支持parse.Convert支持string转string[]

`collections v0.2.0` / 2022.12.12

    1. 新增：支持数据库varchar类型，按,号分割转换成list
    2. 修复：json序列化问题

`webapi v0.2.0` / 2022.12.12

1. 新增：`header自动绑定`
2. 新增：`IActionFilter过滤器`
3. 新增：`批量注册路由`方法`webapi.RegisterRoutes`
4. 调整：重命名handleMiddleware
5. 调整：mvc控制器，路由名称转为小写
6. 调整：mvc控制器，增加对Action的参数设置
7. 修复：Response输出报文时，非Go基础类型，均转成json
8. 修复：controller.BaseController初始化时的错误
9. 修复：异常内容转成string的错误

## farseer-go`v0.2.0` by 2022.12.06
```go
fs              v0.1.0 -> v0.2.0 / 2022.12.06
collections     v0.1.0 ->
mapper          v0.1.0 -> v0.1.1 / 2022.09.25
cache           v0.1.0 -> v0.1.1 / 2022.12.20
cacheMemory     v0.1.0 ->
redis           v0.1.0 ->
data            v0.1.0 ->
eventBus        v0.1.0 ->
queue           v0.1.0 ->
tasks           v0.1.0 ->
utils           v0.1.0 -> v0.2.0 / 2022.12.06
webapi          v0.0.0 -> v0.1.0 / 2022.12.06
etcd            v0.0.0 ->
rabbit          v0.0.0 ->
fShedule        v0.0.0 ->
async           v0.0.0 ->
elasticSearch   v0.0.0 ->
```
`fs v0.2.0` / 2022.12.06

1. 新增：types增加判断是否为内置集合类型
2. 新增：types增加获取函数的出参、入参
3. 新增：types增加判断是否为DTO参数
4. 新增：`WebException异常`
5. 新增：ApiResponse增加转Json、转Bytes方法
6. 新增：增加模块依赖检查功能
7. 修复：json序列化问题

`mapper v0.1.1` / 2022.09.25
    
1. 调整：ToPageList支持：ListAny、List[xx]、[]xx转List[yy]

`utils v0.2.0` / 2022.12.06

1. 调整：http.client改为链式调用

## farseer-go`v0.1.0` by 2022.09.20
```go
fs              v0.0.0 -> v0.1.0 / 2022.09.20
collections     v0.0.0 -> v0.1.0 / 2022.09.06
mapper          v0.0.0 -> v0.1.0 / 2022.09.02
cache           v0.0.0 -> v0.1.0 / 2022.09.11
cacheMemory     v0.0.0 -> v0.1.0 / 2022.09.02
redis           v0.0.0 -> v0.1.0 / 2022.09.09
data            v0.0.0 -> v0.1.0 / 2022.09.08
eventBus        v0.0.0 -> v0.1.0 / 2022.09.11
queue           v0.0.0 -> v0.1.0 / 2022.09.11
tasks           v0.0.0 -> v0.1.0 / 2022.09.11
utils           v0.0.0 -> v0.1.0 / 2022.09.11
webapi          v0.0.0 ->
etcd            v0.0.0 ->
rabbit          v0.0.0 ->
fShedule        v0.0.0 ->
async           v0.0.0 ->
elasticSearch   v0.0.0 ->
```

    我们非常高兴地发布第一个外部版本号v0.1.0
    在此之前，我们的团队一直在努力为框架添加各种功能，以满足业务系统的需求。
    3个月后，我们将常用的功能添加到框架中，并通过初步测试验证。
    因此，我们的团队决定发布这个版本号。 
    需要注意的是，在v1到来之前，v0，仍然会有很多小问题。但是，我们会努力迅速修复发现的问题。
    如果你在使用过程中发现任何bug，也请发问题或PR，谢谢。

