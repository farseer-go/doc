# redis 缓存
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/redis)

> 包：`"github.com/farseer-go/redis"`
>
> 模块：`redis.Module`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/redis)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/redis)
![](https://img.shields.io/github/v/release/farseer-go/redis)
[![codecov](https://img.shields.io/codecov/c/github/farseer-go/redis)](https://codecov.io/gh/farseer-go/redis)
![](https://img.shields.io/github/languages/code-size/farseer-go/redis)
[![Build](https://github.com/farseer-go/redis/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/redis/actions/workflows/build.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/redis)

## 1、概述
Redis与数据库一样，几乎是项目的标配。在farseer-go框架使用redis组件也是非常简单。

## 2、配置
_./farseer.yaml_
```yaml
Redis:
  default: "Server=192.168.1.8:6379,DB=1,Password=123456,ConnectTimeout=600000,SyncTimeout=10000,ResponseTimeout=10000"
```
我们定义了：`default`的配置，请注意这个default将用于获取redis对象时使用。

## 3、获取redis对象
### 3.1、注入的方式
```go
// RedisContext Redis实例
var RedisContext *redisContext

type redisContext struct {
	redis.IClient `inject:"default"` // 使用farseer.yaml的Redis.default配置节点，并自动注入
}

// InitRedisContext 初始化上下文
func InitRedisContext() {
	RedisContext = container.ResolveIns(&redisContext{})
}
```

### 3.2、手动获取
```go
container.Resolve[redis.IClient]("default")
```

## 4、常用的方法
```go
cache:=container.Resolve[redis.IClient]("default")
cache.HashGet("key", "field")
cache.HashGetAll("key")
cache.HashIncrInt("key", "field", 123)
cache.StringSet("key1", "9883")
cache.client.StringGet("key1")
cache.Del("key1")
```
由于方法太多，具体的其它方法，请到查看`redis.IClient`接口

## 5、选举
组件实现了选举的功能：
```go
type IClient interface {
    // Election 选举
    Election(key string, fn func ())
    // GetLeaderId 获取当前LeaderId
    GetLeaderId(key string) int64
}	
```
这里实现的选举功能，是模拟：多个实例争夺一个执行者。（并没有任何环境的计算）

在调用`Election`函数，抢到锁时。会调用fn参数。并且会自动续约，直到应用退出。
当应用退出时，续约期满后。其它实例这时就会抢到这个锁，继续执行fn。

通常的场景是：希望应用在运行期间持续执行某项task，如果当前应用异常退出，则由其它实例顶替继续执行。`（高可用）`

## 6、发布订阅
请查看[事件总线](/other/eventBus.md)篇

## 7、多租户
定义多租户上下文
```go
package context
var mapRedisContext = make(map[int]redis.IClient)

// RedisContext Redis实例
func RedisContext() redis.IClient {
	return mapRedisContext[common.GetCompanyId()]
}

// InitRedisContext 初始化上下文
func InitRedisContext(companyId int) {
	mapRedisContext[companyId] = container.Resolve[redis.IClient](parse.ToString(companyId))
}
```

```go
// 注册Redis
redis.Register(strCompanyId, item.Conn.Redis)
context.InitRedisContext(item.Id)
```

## 8、事务
```go
cache := container.Resolve[redis.IClient]("default")
cache.Transaction(func(){
    cache.StringSet("key1", "9883")
    cache.Del("key1")
})
```
开启事务的方式也很简单。在方法`Transaction`内执行的Redis操作，都会处于同一个事务中。
## 9、管道
```go
redisClient := container.Resolve[redis.IClient]("default")
redisClient.Pipeline(func(){
    redisClient.StringSet("key1", "9883")
    redisClient.Del("key1")
})
```
开启管道的方式也很简单。在方法`Pipeline`内执行的Redis操作，都会处于同一个管道中。

## 10、批量读取数据
批量读取数据并填充到map
```go
	redisClient := container.Resolve[redis.IClient]("default")
	cmdResult, _ := redisClient.Pipeline(func() {
		lstKey.Foreach(func(key *string) {
			redisClient.HashGetAll(*key)
		})
	})
	var m map[string]string
	cmdResult.Fill(&m)
```

批量读取数据并填充到List
```go
	redisClient := container.Resolve[redis.IClient]("default")
	cmdResult, _ := redisClient.Pipeline(func() {
		lstKey.Foreach(func(key *string) {
			redisClient.HashGetAll(*key)
		})
	})
	var lst collections.List[string]
	cmdResult.Fill(&lst)
```