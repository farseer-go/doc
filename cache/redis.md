# redis 缓存
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/redis)
> 包：`"github.com/farseer-go/cache"`、`"github.com/farseer-go/redis"`
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

redis缓存与进程缓存操作方式类似（他们使用同一个CacheManage对象来管理缓存）

redis缓存是把数据集合缓存到redis hash结构中，集合中的数据`需要有唯一标识`。

在使用之前，需要先对这个集合做初始化操作，在这里，我们以一个PO结构体来演示：
_po.go_
```go
type po struct {
	Name string     // 这里Name具有唯一标识
	Age  int
}
```

## 初始化操作
_SetProfilesInRedis定义_
```go
func SetProfilesInRedis[TEntity any](key string, redisConfigName string, uniqueField string, redisExpiry time.Duration)
```
- `key`：这批数据的key，用于区分不同数据集合。
- `redisConfigName`：redis的配置key，在./farseer.yaml 的 Redis.XXX 节点
- `uniqueField`：数据集合中，用于区分Item的唯一值的字段名称
- `redisExpiry`：缓存失效时间

_使用演示_
```go
fs.Initialize[cacheMemory.Module]("进程缓存演示")
cache.SetProfilesInRedis[po]("test","default", "Name", 0)
```
- 第2个参数：对应./farseer.yaml 的 `Redis.default` 节点
- 第3个参数：对应po结构的Name字段名称
- 第4个参数：0代表不失效

## 获取对象
在读写这个缓存集合前，我们需要先获取到这个集合的对象：
```go
cacheManage := cache.GetCacheManage[po]("test")
```

## 方法
### 1、Get
获取缓存的集合对象
```go
func (receiver CacheManage[TEntity]) Get() collections.List[TEntity]
```
### 2、ExistsKey
缓存集合是否存在：如果没初始过Key，或者Key缓存已失效，都会返回false
```go
func (receiver CacheManage[TEntity]) ExistsKey() bool
```
### 3、ExistsItem
集合中是否存在，`cacheId` 为 初始化时传入的`uniqueField`
```go
func (receiver CacheManage[TEntity]) ExistsItem(cacheId string) bool
```
### 4、Set
缓存整个集合，将覆盖原有集合（如果有数据）
```go
func (receiver CacheManage[TEntity]) Set(val ...TEntity)
```
### 5、SaveItem
更新item数据到集合
```go
func (receiver CacheManage[TEntity]) SaveItem(newVal TEntity)
```
### 6、Remove
移除集合中的item数据
```go
func (receiver CacheManage[TEntity]) Remove(cacheId string) 
```
### 7、Count
获取集合内的数量
```go
func (receiver CacheManage[TEntity]) Count() int 
```
### 8、Clear
清空数据
```go
func (receiver CacheManage[TEntity]) Clear()
```

## 演示
```go
fs.Initialize[cacheMemory.Module]("进程缓存演示")
cache.SetProfilesInRedis[po]("test","default", "Name", 0)
cacheManage := cache.GetCacheManage[po]("test")

// 缓存了2项数据
cacheManage.Set(po{Name: "steden", Age: 18}, po{Name: "steden2", Age: 19})

// 获取Name = "steden" 的数据
item1, _ := cacheManage.GetItem("steden")

// 添加新的item
cacheManage.SaveItem(po{Name: "steden3", Age: 121})

// 移除Name = "steden" 的数据
cacheManage.Remove("steden")

// 2
cacheManage.Count()

// 清空缓存内的所有item
cacheManage.Clear()                         
```