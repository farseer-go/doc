# redis 缓存
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/redis)

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



## 多租户
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

item.Conn.Redis："Server=127.0.0.1:6379,DB=0,Password=123456,ConnectTimeout=600000,SyncTimeout=10000,ResponseTimeout=10000"
