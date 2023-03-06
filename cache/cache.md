# cache 集合缓存
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/cacheMemory)

集合缓存支持`将数据集合缓存到进程或Redis`，与直接操作Redis的区别是：集合缓存支持数据库自动同步到Redis或本地。

这是一种更加抽象的使用，可以让我们遮蔽缓存技术。

> 很多时候，我们在数据库中有一张表，为了提高性能会全量同步到Redis（或本地缓存）。读取的时候优先读Redis（或本地缓存）
> 
> 与此同时，如果Redis数据不存在，则从数据库中读取然后缓存到Redis。
> 
> 这种场景下就可以使用集合缓存模块来实现自动同步

集合中的数据`需要有唯一标识`。

在使用之前，需要先对这个集合做初始化操作，在这里，我们以一个PO结构体来演示：
_po.go_
```go
type po struct {
	Name string     // 这里Name具有唯一标识
	Age  int
}
```

## 1、定义集合规则
_SetProfiles定义_
### 1.1、本地缓存
> 包：`"github.com/farseer-go/cacheMemory"` 模块：`cacheMemory.Module`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/cacheMemory)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/cacheMemory)
![](https://img.shields.io/github/v/release/farseer-go/cacheMemory)
[![codecov](https://img.shields.io/codecov/c/github/farseer-go/cacheMemory)](https://codecov.io/gh/farseer-go/cacheMemory)
![](https://img.shields.io/github/languages/code-size/farseer-go/cacheMemory)
[![Build](https://github.com/farseer-go/cacheMemory/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/cacheMemory/actions/workflows/test.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/cacheMemory)

```go
// SetProfiles 设置内存缓存（集合）
// key：这批数据的key，用于区分不同数据集合。
// uniqueField：数据集合中，用于区分Item的唯一值的字段名称（主键）
// expiry：缓存失效时间
func SetProfiles[TEntity any](key string, uniqueField string, expiry time.Duration) cache.ICacheManage[TEntity] {
```

_使用演示_
```go
fs.Initialize[cacheMemory.Module]("进程缓存演示")
cacheMemory.SetProfiles[po]("test", "Name", 0)
```
?> 第2个参数Name对应po结构的Name字段名称，第3个参数0代表不失效

### 1.2、Redis缓存
> 包：`"github.com/farseer-go/redis"` 模块：`redis.Module`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/redis)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/redis)
![](https://img.shields.io/github/v/release/farseer-go/redis)
[![codecov](https://img.shields.io/codecov/c/github/farseer-go/redis)](https://codecov.io/gh/farseer-go/redis)
![](https://img.shields.io/github/languages/code-size/farseer-go/redis)
[![Build](https://github.com/farseer-go/redis/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/redis/actions/workflows/build.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/redis)
```go
// SetProfiles 设置缓存（集合）
// key：这批数据的key，用于区分不同数据集合。
// uniqueField：数据集合中，用于区分Item的唯一值的字段名称（主键）
// expiry：缓存失效时间
// redisConfigName：farseer.yaml的Redis.xx配置名称
func SetProfiles[TEntity any](key string, uniqueField string, expiry time.Duration, redisConfigName string) cache.ICacheManage[TEntity] {
```

_使用演示_
```go
fs.Initialize[redis.Module]("进程缓存演示")
redis.SetProfiles[po]("test", "Name", 0, "default")
```
?> 第2个参数Name对应po结构的Name字段名称，第3个参数0代表不失效，第4个参数"default"代表farseer.yaml的Redis.default配置名称

!> 只在定义时，需要区分是进程缓存还是Redis缓存。后续使用都定义在cache包：`cache.ICacheManage[TEntity any]`接口。

## 2、获取对象
在读写这个缓存集合前，我们需要先获取到这个集合的对象：
```go
cacheManage := container.Resolve[cache.ICacheManage[po]]("test")
```
cacheManage是`ICacheManage`接口类型，抽象了对缓存集合的各种操作

?> 这里我们通过IOC容器的方式获取，cache.ICacheManage接口也是支持注入的方式获取。

## 3、方法
### 3.1、ICacheManage接口的定义
```go
type ICacheManage[TEntity any] interface {
	// SetListSource 集合数据不存在时，则通过getListSourceFn获取
	SetListSource(getListSourceFn func() collections.List[TEntity])
	// SetItemSource 元素不存在时，则通过getItemSourceFn获取
	SetItemSource(getItemSourceFn func(cacheId any) (TEntity, bool))
	// EnableItemNullToLoadAll 元素不存在时，自动读取集合数据源
    EnableItemNullToLoadAll()
	// Get 获取缓存数据
	Get() collections.List[TEntity]
	// Single 获取单个对象
	Single() TEntity
	// GetItem 从集合中获取指定cacheId的元素
	GetItem(cacheId any) (TEntity, bool)
	// Set 缓存整个集合，将覆盖原有集合（如果有数据）
	Set(val ...TEntity)
	// SaveItem 更新item数据到集合
	SaveItem(newVal TEntity)
	// Remove 移除集合中的item数据
	Remove(cacheId string)
	// Clear 清空数据
	Clear()
	// ExistsKey 缓存集合是否存在：如果没初始过Key，或者Key缓存已失效，都会返回false
	ExistsKey() bool
	// ExistsItem 缓存是否存在
	ExistsItem(cacheId string) bool
	// Count 获取集合内的数量
	Count() int
}
```

### SetListSource
设置整个集合的数据源，比如这个集合的数据是来自数据库的。当获取缓存数据时，发现没有缓存数据（或失效），则可以通过这个源重新拿到数据并缓存到本地
```go
cacheManage.SetListSource(func() collections.List[taskGroup.DomainObject] {
    var lst collections.List[taskGroup.DomainObject]
    repository.TaskGroup.ToList().MapToList(&lst)
    return lst
})
```

此段代码，会从`repository`仓储中拿到整个表的数据，当我们获取缓存整合时，如果当前没有缓存数据，则会调用SetListSource的func来缓存数据。

### SetItemSource
设置集合中每一项的数据源，当缓存集合中的这一项数据不存在时，会调用SetItemSource的func来获取。
```go
cacheManage.SetItemSource(func(cacheId any) (taskGroup.DomainObject, bool) {
    po := repository.TaskGroup.Where("Id = ?", cacheId).ToEntity()
    if po.Id > 0 {
        return mapper.Single[taskGroup.DomainObject](&po), true
    }
    var do taskGroup.DomainObject
    return do, false
})
```

### 3.2、SetSyncSource
设置将缓存的数据同步到你需要的位置，比如同步到数据库
```go
cacheManage.SetSyncSource(60*time.Second, func(do taskGroup.TaskEO) {
    po := mapper.Single[model.TaskPO](&do)
    repository := data.NewContext[taskRepository]("default")
    var result bool
    result = repository.Task.Where("Id = ?", po.Id).Update(po) > 0
    if !result {
        result = repository.Task.Insert(&po) == nil
    }
})
```
如上，定义每隔60秒，将缓存中的数据更新到数据库中

### 3.3、SetClearSource
设置清理缓存中的数据
```go
cacheManage.SetClearSource(60*time.Second, func(do taskGroup.TaskEO) bool {
    return do.IsFinish()
})
```
如上，定义每隔60秒，将IsFinish = true的数据，清除

### 3.4、EnableItemNullToLoadAll
当开启此选项时，并且未设置`SetItemSource`函数，当要从缓存集合中获取的一项数据不存在时，则会重新缓存整个数据（调用SetListSource的func来缓存数据）
```go
cacheManage.EnableItemNullToLoadAll()
```

## 4、演示
```go
fs.Initialize[cacheMemory.Module]("进程缓存演示")
cacheMemory.SetProfiles[po]("test", "Name", 0)
cacheManage := container.Resolve[cache.ICacheManage[po]]("test")

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