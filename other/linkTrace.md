# linkTrace 链路追踪
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/linkTrace)

> 包：`"github.com/farseer-go/linkTrace"`
>
> 模块：`linkTrace.Module`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/linkTrace)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/linkTrace)
![](https://img.shields.io/github/v/release/farseer-go/linkTrace)
![Codecov](https://img.shields.io/codecov/c/github/farseer-go/linkTrace)
![](https://img.shields.io/github/languages/code-size/farseer-go/linkTrace)
![](https://img.shields.io/github/directory-file-count/farseer-go/linkTrace)
![](https://goreportcard.com/badge/github.com/farseer-go/linkTrace)

## 1、概述
在farseer-go的v0.9版本，终于迎来了链路追踪组件。

链路追踪可以让我们实现在多个服务（Webapi、http、grpc）之间调用时，可以统计整条链路的调用情况，如每个服务的执行耗时、执行SQL耗时、Redis耗时、ES耗时等等。

例如：我们的系统上线了：A、B两个http服务。用户访问A服务的时候，A服务(访问)--->B服务，并且其内部还执行了数据库、缓存：

`某用户`---外网http请求--->`A服务`---（**读取数据库**--->**写入Redis**）---内网http请求--->`B服务`---（**读取Redis**--->**更新数据库**）--->结束

?>这个请求涉及到两个服务。如果能详细知道每个服务、数据库、缓存的执行耗时，就可以快速的帮我们定位问题或优化性能。

从用户的角度是，这个请求耗时了200ms，而链路追踪能将这200ms分解出更详细的记录。（还记得我们手动打日志来计算耗时吗）

## 2、自动埋点
### 2.1、入口
链路统计的起点，这些组件在收到请求后，会开始跟踪后续的操作。
* `webapi`（api服务）
* `rabbit.consumer`（rabbit消费）
* `queue`（本地队列）
* `eventBus`（事件总线消费）
* `task`（本地任务执行）
* `fSchedule`（调度中心任务执行）

### 2.2、执行明细
从入口开始跟踪后，以下组件支持自动记录每个执行情况
* `data`（数据库组件）
* `redis`（redis缓存）
* `es`（elasticSearch组件）
* `rabbit.send`（rabbit消息发送）
* `etcd`（etcd组件）
* `httpClient`（utils组件提供的httpClient）

!> 这些组件必须是由2.1组件发起的才会被追踪

## 3、手动埋点
在需要单独埋点的位置，可以使用代码来调用：
```go
traceHand := linkTrace.TraceHand("删除历史记录")
defer traceHand.End(nil)
```
这段代码，会记录当前函数执行的耗时统计。

## 4、配置
_./farseer.yaml_
```go
LinkTrace:
  Enable: true      // 记录链路日志到ES
  PrintLog: true    // 记录打印到控制台（即使Enable=false也会打印）
  ES: "Server=http://192.168.1.8:9200,Username=es,Password=123456,ReplicasCount=1,ShardsCount=1,RefreshInterval=5,IndexFormat=yyyy_MM"
```
链路追踪的记录信息最终将写入到ES。所以需要大家提供ES的配置。

如果没有ES，也可以只打印日志到控制台。

## 5、打印效果：
```sql
2023-10-17 23:26:22 【链路追踪】TraceId:251427514347999232，耗时：32.224ms，http://127.0.0.1:888/home/info：
1(214µs)：[Database]耗时：8.99ms， SELECT count(*) FROM `user` WHERE `id` = 1
2(477µs)：[Database]耗时：7.79ms， SELECT * FROM `online` WHERE `login_name` = 'admin' LIMIT 1
3(81µs)：[Database]耗时：8.315ms， UPDATE `online` SET `activity_at`='2023-10-17 23:26:22.02' WHERE login_name = 'admin'
4(48µs)：[Redis]耗时：6.181ms， StringSet user`
```
从这个日志，我们可以看到，请求接口时，整个接口耗时了`32.224ms`，并详细记录了这些耗时执行了哪些。（执行了4个操作）

这些操作都是由框架自动记录的，并不需我们写一行代码。

另外，在第2列的214us、477us、81us、48us，代表当前执行与上一个执行之间，有214us执行了其它逻辑。如果这个数值过大，说明需要手动埋点了。