# mapper对象转换
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/mapper)
> 包：`"github.com/farseer-go/mapper"`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/mapper)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/mapper)
![](https://img.shields.io/github/v/release/farseer-go/mapper)
[![codecov](https://img.shields.io/codecov/c/github/farseer-go/mapper)](https://codecov.io/gh/farseer-go/mapper)
![](https://img.shields.io/github/languages/code-size/farseer-go/mapper)
[![Build](https://github.com/farseer-go/mapper/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/mapper/actions/workflows/test.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/mapper)

## 1、概述
我们在开发当中经常会遇到`DTO实体与PO数据库实体`、`实体集合与实体集合`、以及各种各样的`相互转换`问题。

该组件很好的解决了上述转换过程中需要每个字段单独赋值的繁琐代码。

组件支持 `Map之间互转`，支持`实体、Map互转`，支持 `数组之间互转`，支持 `数组、List互转`，支持 `List之间互转` 。

另一个重要的功能是支持`子属性`的转换：一个实体包含子实体，支持转换到一个展开的实体对象。

## 2、使用场景
一般来说，在应用系统中都会有很多实体类对象，比如数据库的实体类。

有时候从数据库查出来的对象，我们不希望把所有字段都返回给前端（接口）

比如UserPO数据库对象，其中包含了密码字段，我们要把这个对象返回给前端，肯定不会把密码字段返回出去的。

这个时候可能会定义一个UserDTO实体（没有密码字段）。这个时候我们希望直接通过简单的方法，让UserPO数据直接转换到UserDTO中。

## 3、示例类型
```go
type ClientVO struct {
    Id   int64
    Ip   string
    Name string
}

type UserVO struct {
    Id   int64
    Name string
}

type TaskDO struct {
	Id           int
	Client       ClientVO   // struct结构
	Status       State
	UserId       int64
	UserName     string
	Data         collections.Dictionary[string, string]
	CreateAt     time.Time
	IsEnable     bool
}

type TaskDTO struct {
	Id           int
	ClientId     int64
	ClientIp     string
	ClientName   string
	Status       State
	User         UserVO // struct结构
	Data         collections.Dictionary[string, string]
	CreateAt     time.Time
	UpdateAt     time.Time
	IsEnable     bool
}
```
这是两个对象，我们的目的是要让TaskDO、TaskDTO之间能快速的转换。

可以看出在TaskDO.Client、TaskDTO.User两个子属性，他们都是struct结构，我们的目的是让子属性也能支持这种优雅的转换

## 4、Single
单个对象之间的转换，对象可以是：struct、map

_函数定义_
```go
// Single 单个转换
func Single[TEntity any](fromObjPtr any) TEntity
```

_用法：_
```go
dto := TaskDTO{
    Id:         1,
    ClientId:   1000,
    ClientIp:   "127.0.0.1",
    ClientName: "node",
    Status:     Pending,
    User: UserVO {
    Id:   88,
    Name: "steden",
    },
    Data: collections.NewDictionaryFromMap(map[string]string{"age": "18", "price": "88.88"}),
}

taskDO := mapper.Single[TaskDO](dto)
```
将`TaskDTO` 成 `TaskDO`对象


## 5、Array
将切片对象转换成另一个切片对象

_函数定义_
```go
// Array 数组转换
// fromSlice=数组切片
func Array[T any](fromSlice any) []T
```

_用法：_
```go
arrDto := []TaskDTO{{
		Id:         1,
		ClientId:   1000,
		ClientIp:   "127.0.0.1",
		ClientName: "node",
		Status:     Pending,
		User:       UserVO{Id: 88, Name: "steden"},
		Data:       collections.NewDictionaryFromMap(map[string]string{"age": "18", "price": "88.88"}),
	}, {
		Id:         2,
		ClientId:   1000,
		ClientIp:   "127.0.0.1",
		ClientName: "node",
		Status:     Pending,
		User:       UserVO{Id: 20, Name: "steden1"},
		Data:       collections.NewDictionaryFromMap(map[string]string{"age": "18", "price": "88.88"}),
	}}

arrDO := mapper.Array[TaskDO](arrDto)
```
将`[]TaskDTO` 转 `[]TaskDO`切片


## 6、ToMap
将`结构`对象转成`map`类型

_函数定义_
```go
// ToMap 结构体转Map
func ToMap[K comparable, V any](entity any) map[K]V
```

_用法：_
```go
dto := TaskDTO{
    Id:         1,
    ClientId:   1000,
    ClientIp:   "127.0.0.1",
    ClientName: "node",
    Status:     Pending,
    User: UserVO{Id: 20, Name: "steden1"},
    Data: collections.NewDictionaryFromMap(map[string]string{"age": "18", "price": "88.88"}),
}
dic := mapper.ToMap[string, any](dto)
```

将`TaskDTO` 转 `map[string]any类型`

## 7、ToPageList
collections.PageList集合类型转换

_函数定义_
```go
// ToPageList 转换成core.PageList
// fromSlice=数组切片
func ToPageList[TEntity any](sliceOrListOrListAny any, recordCount int64) collections.PageList[TEntity]
```

_用法：_
```go
arrDto := []TaskDTO{{
    Id:         1,
    ClientId:   1000,
    ClientIp:   "127.0.0.1",
    ClientName: "node",
    Status:     Pending,
    User:       UserVO{Id: 88, Name: "steden"},
    Data:       collections.NewDictionaryFromMap(map[string]string{"age": "18", "price": "88.88"}),
}, {
    Id:         2,
    ClientId:   1000,
    ClientIp:   "127.0.0.1",
    ClientName: "node",
    Status:     Pending,
    User:       UserVO{Id: 20, Name: "steden1"},
    Data:       collections.NewDictionaryFromMap(map[string]string{"age": "18", "price": "88.88"}),
}}

lst := mapper.ToPageList[TaskDO](arrDto, 10)
```

collections.PageList类型
```go
// PageList 用于分页数组，包含总记录数
type PageList[TData any] struct {
	// 总记录数
	RecordCount int64
	// 数据列表
	List List[TData]
}
```
这是带记录总数的数据集

将`collections.PageList[TaskDTO]` 转 `collections.PageList[TaskDO]`

## 8、方法ToList

_函数定义_
```go
// 转换成List
func ToList[TEntity any](sliceOrListOrListAny any) collections.List[TEntity] 
```

_用法：_
```go
arrDto := []TaskDTO{{
    Id:         1,
    ClientId:   1000,
    ClientIp:   "127.0.0.1",
    ClientName: "node",
    Status:     Pending,
    User:       UserVO{Id: 88, Name: "steden"},
    Data:       collections.NewDictionaryFromMap(map[string]string{"age": "18", "price": "88.88"}),
}, {
    Id:         2,
    ClientId:   1000,
    ClientIp:   "127.0.0.1",
    ClientName: "node",
    Status:     Pending,
    User:       UserVO{Id: 20, Name: "steden1"},
    Data:       collections.NewDictionaryFromMap(map[string]string{"age": "18", "price": "88.88"}),
}}
lst := collections.NewList(arrDto...)
lstDO := mapper.ToList[TaskDO](lst)
```

将`collections.List[TaskDTO]` 转 `collections.List[TaskDO]`

## 9、ToListAny

_函数定义_
```go
// 切片转ToListAny
func ToListAny(sliceOrList any) collections.ListAny
```
_用法：_
```go
arrDto := []TaskDTO{{
    Id:         1,
    ClientId:   1000,
    ClientIp:   "127.0.0.1",
    ClientName: "node",
    Status:     Pending,
    User:       UserVO{Id: 88, Name: "steden"},
    Data:       collections.NewDictionaryFromMap(map[string]string{"age": "18", "price": "88.88"}),
}, {
    Id:         2,
    ClientId:   1000,
    ClientIp:   "127.0.0.1",
    ClientName: "node",
    Status:     Pending,
    User:       UserVO{Id: 20, Name: "steden1"},
    Data:       collections.NewDictionaryFromMap(map[string]string{"age": "18", "price": "88.88"}),
}}
listAny := mapper.ToListAny(arrDto)
```
将`[]TaskDTO` 转 `collections.ListAny`

10、忽略字段
```go
type UserVO struct {
    Id   int64  `mapper:ignore`
    Name string
}
```
在将其它类型转换成UserVO类型时，UserVO.Id将忽略，不会转换