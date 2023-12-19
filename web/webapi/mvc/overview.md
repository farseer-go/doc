# mvc 概述
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/webapi)

> 包："github.com/farseer-go/webapi/controller"

使用webapi的mvc模式，可以`自动注册controller下的所有action`，省去一个个action注册的麻烦

## 1、定义controller
使用mvc模式，需要我们在UI层定义控制器
```go
import (
    "fmt"
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

## 2、注册控制器
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