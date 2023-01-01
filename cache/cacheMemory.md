# cacheMemory 进程缓存
> 包：`"github.com/farseer-go/cache"`、`"github.com/farseer-go/cacheMemory"`

> 模块：`cacheMemory.Module`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/cacheMemory)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/cacheMemory)
![](https://img.shields.io/github/v/release/farseer-go/cacheMemory)
[![codecov](https://img.shields.io/codecov/c/github/farseer-go/cacheMemory)](https://codecov.io/gh/farseer-go/cacheMemory)
![](https://img.shields.io/github/languages/code-size/farseer-go/cacheMemory)
[![Build](https://github.com/farseer-go/cacheMemory/actions/workflows/go.yml/badge.svg)](https://github.com/farseer-go/cacheMemory/actions/workflows/go.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/cacheMemory)

进程缓存支持`将数据集合缓存到本地`，集合中的数据`需要有唯一标识`。

在使用之前，需要先对这个集合做初始化操作，在这里，我们以一个PO结构体来演示：
_po.go_
```go
type po struct {
	Name string     // 这里Name具有唯一标识
	Age  int
}
```

## 初始化操作
_SetProfiles定义_
```go
func SetProfiles[TEntity any](key string, uniqueField string, expiry time.Duration)
```
- `key`：这批数据的key，用于区分不同数据集合。
- `uniqueField`：数据集合中，用于区分Item的唯一值的字段名称（主键）
- `expiry`：缓存失效时间

_使用演示_
```go
fs.Initialize[cacheMemory.Module]("进程缓存演示")
cacheMemory.SetProfiles[po]("test", "Name", 0)
```
?> 第二个参数Name对应po结构的Name字段名称，第三个参数0代表不失效

## 获取对象
在读写这个缓存集合前，我们需要先获取到这个集合的对象：
```go
cacheManage := container.Resolve[cache.ICacheManage[po]]("test")
```
?> 这里我们通过IOC容器的方式获取，cache.ICacheManage接口也是支持注入的方式获取。

## 方法
### ICacheManage接口的定义
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
设置整个集合的数据源，比如这个集合的数据是来自数据库的。当获取缓存数据时，发现本地没有缓存数据（或失效），则可以通过这个源重新拿到数据并缓存到本地
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
### EnableItemNullToLoadALl
当开启此选项时，并且未设置`SetItemSource`函数，当要从缓存集合中获取的一项数据不存在时，则会重新缓存整个数据（调用SetListSource的func来缓存数据）
```go
cacheManage.EnableItemNullToLoadAll()
```
## 演示
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