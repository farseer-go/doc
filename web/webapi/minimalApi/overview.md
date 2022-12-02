# minimalApi 概述

## 介绍
> minimalApi是一种`极简`、`优雅`的风格，`不需要依赖任何参数`，完全就是你自身的应用代码

只需要在`webapi.Run()`启动前注册就可以运行了，不需要你特意去依赖`http.Request`、`http.Response`，极大的简化了应用代码。

_最佳实践_

?> `minimalApi`是作者推荐的方式，因为它不需要依赖`web.Controller`，也就不需要专门建一个UI层。是一种`动态api方案`。

## 注册动态Api
使用`minimalApi`非常简单，只需要在`web.Run()`执行前调用`webapi.RegisterXXX`，注册有以下几种：
- webapi.`RegisterPOST`
- webapi.`RegisterGET`
- webapi.`RegisterPUT`
- webapi.`RegisterDELETE`

参数前2个为必传，params参数为可选
```go
func RegisterPOST(route string, actionFunc any, params ...string)
```
- route：路由url，如传入："/projectgroup/tolist"时，则完整URL为：http://localhost:8888/projectgroup/tolist
- actionFunc：可以传入任意函数，为Api的逻辑。
- params：对入参为多参数时的一种命名补充（详细后面会说到）

### 注册示例：
```go
webapi.RegisterPOST("/mini/hello1", testMiniapi.Hello1)
webapi.RegisterPOST("/projectgroup/tolist", projectGroupApp.ToPageList, "pageSize", "pageIndex")
webapi.Run()
```
## 入参
_pageSizeRequest.go 示例中，用到的Model_
```go
type PageSizeRequest struct {
	PageSize  int
	PageIndex int
}
```
### 1、model(dto)

> 当只有`1个`入参、且为`struct`类型时，则会被认为是`model(dto)`模型，如
```go
func Hello1(req PageSizeRequest) string { }
```
这里的req就是`model(dto)`模型，支持前端以`application/json`或`x-www-form-urlencoded`方式传值。

?> 如果是`application/json`，则会自动被反序列化成model，如果是`x-www-form-urlencoded`，则会将每一项的key/value匹配到model字段中

### 2、普通入参

> 当有`多个参数`，或`1个参数`且`不是struct类型`时，则会判定为`普通入参`
```go
func Hello3(pageSize int, pageIndex int) PageSizeRequest { }
```

这里有`pageSize`、`pageIndex`两个入参。

同样支持前端以`application/json`或`x-www-form-urlencoded`方式传值。

?> 如果是`application/json`，则会解析json进行匹配，如果是`x-www-form-urlencoded`，同样会进行匹配。

!> 由于go无法通地反射函数来获取到参数的名称，因此需要显示指定参数的命名，以此来做到参数匹配。这就需要在注册时，显示指定：
```go
webapi.RegisterPOST("/projectgroup/tolist", projectGroupApp.ToPageList, "pageSize", "pageIndex")
```
?> "pageSize", "pageIndex"就是告诉webapi，`入参的命名`


## 出参
### 1、1个struct类型返回值

> 当只有`1个`出参、且为`struct`类型时，则会被认为是`model(dto)`模型，如
```go
func Hello3(pageSize int, pageIndex int) request.PageSizeRequest {
    return request.PageSizeRequest{
        PageSize:  pageSize,
        PageIndex: pageIndex,
    }
}
```
这时会把`request.PageSizeRequest`序列化成json后输出

_运行结果：_

```json
{
    "PageSize": 10,
    "PageIndex": 1
}
```
### 2、1个非struct类型返回值

> 当只有`1个`出参、且不是`struct`类型时，则会直接输出，如
```go
func Hello1(req request.PageSizeRequest) string {
    return fmt.Sprintf("hello world pageSize=%d，pageIndex=%d", req.PageSize, req.PageIndex)
}
```
_运行结果：_
```text
"hello world pageSize=3，pageIndex=1"
```
 
_多个出参_
```go
func Hello4(pageSize int, pageIndex int) (int, int) {
    return pageSize, pageIndex
}
```

这时，会将多个返回值组成json数组

_运行结果：_
```json
[ 10, 1 ]
```

## 特殊说明
!> 由于minimalApi不需要依赖controller、httpContext，因此`不支持获取header`，如果需要获取，可以使用`mvc模式`。

?> 在不需要header的场景下，推荐大家用`minimalApi风格`。