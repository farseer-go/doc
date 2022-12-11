# mvc 概述
> 包："github.com/farseer-go/webapi/controller"

使用webapi的mvc模式，可以`自动注册controller下的所有action`，省去一个个action注册的麻烦

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