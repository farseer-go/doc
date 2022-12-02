# mvc 概述
> 包："github.com/farseer-go/webapi/controller"

## 介绍
> mvc是一种model view controller的风格。webapi只支持model、controller

使用webapi的mvc模式，可以`自动注册controller下的所有action`，省去了一个个action注册的麻烦

## 定义controller
使用mvc模式，需要我们在UI层定义控制器
```go
import (
    "fmt"
    "fops/interfaces/request"
    "github.com/farseer-go/webapi/controller"
)

type TestController struct {
    controller.BaseController // 需要继承controller.BaseController
}

func (r *TestController) Hello1(req request.PageSizeRequest) string {
    return fmt.Sprintf("hello world pageSize=%d，pageIndex=%d", req.PageSize, req.PageIndex)
}

func (r *TestController) Hello2(pageSize int, pageIndex int) request.PageSizeRequest {
    return request.PageSizeRequest{
        PageSize:  pageSize,
        PageIndex: pageIndex,
    }
}
```

## 注册控制器
使用`mvc`非常简单，我们先看下注册方法的定义：
```go
func RegisterController(c controller.IController) { }
```
_注册示例_
```go
webapi.RegisterController(&TestController{}) // 注册控制器
webapi.UseCors()
webapi.UseApiResponse()
webapi.Run()
```

?> `RegisterController`方法会将控制器下的所有Action都一并注册进去。比如示例中的`Hello1`、`Hello2`都会被注册。


## 入参
_pageSizeRequest.go 示例中，用到的Model_
```go
type PageSizeRequest struct {
	PageSize  int
	PageIndex int
}
```
### model(dto)
!> mvc只支持`model模型`的入参，即只允许1个参数、且为`struct`类型时
```go
func Hello1(req PageSizeRequest) string { }
```
这里的req就是`model(dto)`模型，支持前端以`application/json`或`x-www-form-urlencoded`方式传值。

?> 如果是`application/json`，则会自动被反序列化成model，如果是`x-www-form-urlencoded`，则会将每一项的key/value匹配到model字段中


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

## httpContext 上下文
> 通过上下文，我们可以获取很多http的信息，如header

```go
type HttpContext struct {
	Request          *HttpRequest
	Response         *HttpResponse
	Header           collections.Dictionary[string, string]
	Route            *HttpRoute
	URI              *HttpURL
	Method           string
	ContentLength    int64
	Close            bool
	TransferEncoding []string
	ContentType      string
	Exception        any
}
```

### Header
通过r.HttpContext.Header可以获取到所有Header信息
```go
func (r *TestController) Hello3() (TValue string) {
	return r.HttpContext.Header.GetValue("Content-Type")
}
```

### 状态码
如果需要返回自定义的状态码，可以使用WriteCode
```go
r.HttpContext.Response.WriteCode(200)
```