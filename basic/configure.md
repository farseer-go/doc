# configure（配置文件管理）
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/fs)
> 包：`"github.com/farseer-go/fs/configure"`

使用`farseer-go`框架，可以让你有一个统一的配置管理，我们将所有组件的配置统一写在`farseer.yaml`中。

## 1、配置示例
_./farseer.yaml_
```yaml
Database:
  default: "DataType=mysql,PoolMaxSize=50,PoolMinSize=1,ConnectionString=root:123456@tcp(127.0.0.1:3306)/fSchedule_demo?charset=utf8&parseTime=True&loc=Local"
Redis:
  default: "Server=127.0.0.1:6379,DB=15,Password=123456,ConnectTimeout=600000,SyncTimeout=10000,ResponseTimeout=10000"
FSchedule:
  ReservedTaskCount: 20
  PullCount: 100
  WorkCount: 100
  PullInterval: 500
ElasticSearch:
  es: "Server=http://127.0.0.1:9200,Username=es,Password=123456,ReplicasCount=1,ShardsCount=1,RefreshInterval=5,IndexFormat=yyyy_MM"
  LinkTrack: "Server=http://127.0.0.1:9200,Username=es,Password=123456"
WebApi:
  Url: ":888"
Log:
  LogLevel: "Information"
  Component:
    task: false
    cacheManage: false
    webapi: false
    event: true
    httpRequest: false
    queue: true
    fSchedule: true
```
!> 配置文件：`./farseer.yaml` （位置在应用程序根目录中）

使用组件时，你只要负责在`./farseer.yaml`进行配置，组件会根据你写的配置进行实例化。

?> 各组件的配置，会在每个组件说明文档中详细描述

## 2、使用自己的配置
我们提供了一系列手动读取配置的函数：
```go
// GetString 获取配置
func GetString(key string) string
// GetStrings 获取配置
func GetStrings(key string) []string
// GetSlice 获取数组
func GetSlice(key string) []string
// GetMap 读取子节点
func GetMap(key string) map[string]string
// GetInt 获取配置
func GetInt(key string) int
// GetInt64 获取配置
func GetInt64(key string) int64
// GetBool 获取配置
func GetBool(key string) bool
// GetSubNodes 获取所有子节点
func GetSubNodes(key string) map[string]string
// SetDefault 设置配置的默认值
func SetDefault(key string, value any) 
```
## 3、读取配置
读取数据库的default配置：
```go
configure.GetString("Database.default")
```
_运行结果：_

?> "DataType=mysql,PoolMaxSize=50,PoolMinSize=1,ConnectionString=root:123456@tcp(127.0.0.1:3306)/fSchedule_demo?charset=utf8&parseTime=True&loc=Local"

读取FSchedule.ReservedTaskCount配置：
```go
configure.GetInt("FSchedule.ReservedTaskCount")
```
_运行结果：_

?> 20

## 4、设置默认值
有时候，在我们没有对配置文件进行配置时，我们需要一些初始默认值，则可以使用`SetDefault`函数，如：
```go
configure.SetDefault("myConfig.name","steden")
configure.GetString("myConfig.name")
```
_运行结果：_

?> "steden"
> 有时候，在配置文件没有这些配置时，设置默认值很有必要的

## 5、环境变量支持
配置文件默认是支持环境变量的，且优先级别最高，会覆盖配置文件的设置

由于环境变量不支持`.`、`[`、`]`符号的，因此在环境变量中Key需要做转义，比如：

_环境变量_
```
WebApi_Url = :80
```

_./farseer.yaml_
```yaml
WebApi:
  Url: ":888"
```

_读取配置_
```go
// result = :80
configure.GetString("WebApi.Url")
```

代码读取上，还是使用`WebApi.Url`，在环境变量想要覆盖配置，则替换为：`WebApi_Url`