# elasticSearch es ORM组件
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/elasticSearch)

> 包：`"github.com/farseer-go/elasticSearch"`
>
> 模块：`elasticSearch.Module`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/elasticSearch)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/elasticSearch)
![](https://img.shields.io/github/v/release/farseer-go/elasticSearch)
[![codecov](https://img.shields.io/codecov/c/github/farseer-go/elasticSearch)](https://codecov.io/gh/farseer-go/elasticSearch)
![](https://img.shields.io/github/languages/code-size/farseer-go/elasticSearch)
[![Build](https://github.com/farseer-go/elasticSearch/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/elasticSearch/actions/workflows/build.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/elasticSearch)

## 1、概述
Elasticsearch是一个开源的分布式搜索和分析引擎，旨在实现快速、实时的数据搜索、分析和查询。
它是一个基于Apache Lucene的搜索引擎，提供了RESTful API，可以通过HTTP与其进行交互。
Elasticsearch广泛用于构建实时搜索引擎、日志和指标分析、企业应用程序监视和分析等各种用途。


`farseer-go/elasticSearch`可以让应用系统更加优雅的使用rabbit：

- `配置自动化`：通过配置，不需要你手动创建交换器、队列和绑定他们的关系。
- `容器化操作`：使用IOC，我们可以很方便就能取到client。
- `类型自动转换`：支持数据库类型自动转换成Es对应数据类型进行存储。
- `实体对象的便捷操作`：支持对象化操作，将ES的读写操作进行再次封装，使得用户在使用过程中更加便捷。

## 2、配置
_./farseer.yaml_
```yaml
ElasticSearch:
  es: "Server=http://ip:port,Username=es,Password=xxx,ReplicasCount=1,ShardsCount=1,RefreshInterval=5,IndexFormat=yyyy_MM"
```

配置说明：
- Server（服务端设置）
    - `Server`：服务端地址
    - `UserName`：用户名
    - `Password`：密码
    - `ReplicasCount`：副本数量
    - `ShardsCount`：碎片数量
    - `RefreshInterval`：刷新间隔
    - `IndexFormat`：索引格式

也可以动态配置链接字符串
```go
configure.SetDefault("ElasticSearch.esName", "Server=http://localhost:9200,Username=es,Password=123456,ReplicasCount=1,ShardsCount=1,RefreshInterval=5,IndexFormat=yyyy_MM")
```

## 3、Model层
在应用中，我们会定义一系列的model，用来匹配数据结构，比如：

_model.AdminPO.go_
```go
type UserPO struct {
    Id int `es:"primaryKey" es_type:"integer"`
    // 用户名称
    Name string `es_type:"keyword"`
    // 用户年龄
    Age int `es_type:"integer"`
    //公司
    Company string
}
```

框架会将实体数据类型自动转换成符合ES存储的数据类型，也可以显示的指定ES数据类型。数据转换对应类型如下：

```go
// GetEsType 获取elasticsearch对应类型
func GetEsType(val reflect.Type) string {
	typeStr := val.String()
	typeKind := val.Kind()
	if strings.HasSuffix(typeStr, ".Enum") {
		return "byte"
	}
	switch typeStr {
	case "time.Time":
		return "date"
	case "datetime.DateTime":
		return "date"
	}
	switch typeKind {
	case reflect.String:
		return "Keywords"
	case reflect.Bool:
		return "boolean"
	case reflect.Int:
		return "integer"
	case reflect.Int16:
		return "integer"
	case reflect.Int32:
		return "integer"
	case reflect.Int64:
		return "long"
	case reflect.Uint:
		return "integer"
	case reflect.Uint16:
		return "integer"
	case reflect.Uint32:
		return "integer"
	case reflect.Uint64:
		return "long"
	case reflect.Float32:
		return "double"
	case reflect.Float64:
		return "double"
	case reflect.Struct:
		return "object"
	case reflect.Array:
		return "object"
	case reflect.Slice:
		return "object"
	case reflect.Map:
		return "flattened"
	}
	return "byte"
}
```

## 4、elasticSearch.NewContext 初始化上下文

初始化，非常简单，只需要调用`elasticSearch.NewContext`函数，并传入`esName`即可：
```go
var elasticSearchContext = *EsContext

// EsContext 
type EsContext struct {
    // 用户记录
    User elasticSearch.IndexSet[model.UserPO] `es:"index=User_yyyy_MM_dd;alias=user_yyyy_MM_dd;shards=2;replicas=2;refresh=2"`
}

// InitLbsEsContext 初始化上下文
func InitEsContext() {
    elasticSearchContext = elasticSearch.NewContext[EsContext]("esName")
}

```

也可以进行简单初始化

```go
    context := elasticSearch.NewContext[EsContext]("esName")
```

## 5、调用
- 仓储层调用方式
```go
    elasticSearchContext.User.Where("id", 1).ToPageList(pageSize, pageIndex)
```

## 5.1、单实体插入

```go
	po := UserPO{Name: "小强", Age: 10, Id: 12}
	err := context.User.Insert(po)
```
## 5.2、实体数组插入

```go
	var array []UserPO
    po := UserPO{Name: "小强2", Age: 10, Id: 13}
    array = append(array, po)
    err := context.User.InsertArray(array)
```
## 5.3、实体List插入

```go
	list := collections.NewList(UserPO{Name: "小丽", Age: 20, Id: 2}, UserPO{Name: "小王", Age: 30, Id: 3})
    err := context.User.InsertList(list)
```
## 5.3、Select操作查询自定字段数据

```go
    getList := context.User.Select("Name").ToList()
    getName := getList.First().Name
```
## 5.4、Asc 正序排序

```go
    List := context.User.Asc("Id").ToList()
```
## 5.5、Desc 倒序排序

```go
    list := context.User.Desc("Id").ToList()
```
## 5.6、GetIndexName 获取索引名称

```go
    name := context.User.GetIndexName()
```
## 5.7、ToList 获取实体对象列表集合

```go
    list := context.User.ToList()
```
## 5.8、ToList 获取实体对象列表集合

```go
    list := context.User.ToList()
```
## 5.9、ToPageList(pageSize,pageIndex) 获取实体对象分页列表集合

```go
    list := context.User.ToPageList(1, 2)
```
## 5.10、Where(field,value) 筛选条件的使用

```go
    list := context.User.Where("Age", "30").Where("Id", 14).ToList()
```
## 5.11、DelIndex 删除索引

```go
    err := context.User.DelIndex(context.User.GetIndexName())
```
## 5.12、DelData(field_id) 根据实体ID删除对应数据

```go
    err := context.User.DelData("888")
```
## 5.13、SetAliasesName(aliasesName) 设置索引别名

```go
    _ = context.User.SetAliasesName("test_alis_22")
```

## 6、多租户
先定义多租户的上下文
```go
package context
var esContext = make(map[int]*EsContext)

// EsContext 实际业务
type EsContext struct {
	// 账户操作记录
	AccountOprLog elasticSearch.IndexSet[model.AccountOprLogPO] `es:"index=account_opr_log_yyyy_MM;alias=account_opr_log;shards=3;replicas=1;refresh=1"`
}

// InitEsContext 初始化上下文
func InitEsContext(companyId int) {
	esContext[companyId] = elasticSearch.NewContext[EsContext](parse.ToString(companyId))
}

func EsContext() *EsContext {
	return esContext[common.GetCompanyId()]
}
```
动态注册：
```go
// 初始化到ES
elasticSearch.RegisterInternalContext(strCompanyId, item.Conn.ReportDb)
context.InitEsContext(item.Id)
```