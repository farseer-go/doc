# cacheMemory 进程缓存
> 包：`"github.com/farseer-go/cache"`、`"github.com/farseer-go/cacheMemory"`

> 模块：`cacheMemory.Module`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/cacheMemory)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/cacheMemory)
![](https://img.shields.io/github/v/release/farseer-go/cacheMemory)
![](https://img.shields.io/github/languages/code-size/farseer-go/cacheMemory)
![](https://img.shields.io/github/directory-file-count/farseer-go/cacheMemory)
![](https://img.shields.io/github/last-commit/farseer-go/cacheMemory)

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
_SetProfilesInMemory定义_
```go
func SetProfilesInMemory[TEntity any](key string, uniqueField string, memoryExpiry time.Duration)
```
- `key`：这批数据的key，用于区分不同数据集合。
- `uniqueField`：数据集合中，用于区分Item的唯一值的字段名称
- `memoryExpiry`：缓存失效时间

_使用演示_
```go
fs.Initialize[cacheMemory.Module]("进程缓存演示")
cache.SetProfilesInMemory[po]("test", "Name", 0)
```
第二个参数Name对应po结构的Name字段名称，第三个参数0代表不失效

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
cache.SetProfilesInMemory[po]("test", "Name", 0)
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