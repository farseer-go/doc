# mvc 路由
> mvc的路由规则`{area}`/`{strings.ToLower(prefix controller)}`/`{strings.ToLower(action)}`

我们还是以`TestController`为例子：
```go
type TestController struct {
	controller.BaseController
}

func (r *TestController) Hello1(req request.PageSizeRequest) string { }
func (r *TestController) Hello2(pageSize int, pageIndex int) request.PageSizeRequest { }
```

_main.go_
```go
webapi.RegisterController(&TestController{}) // 注册控制器
webapi.Run()
```

注册后的路由地址为（`小写`）：
- http://localhost:8888/test/hello1
- http://localhost:8888/test/hello2

!> 路由的生成规则中，`TestController`则会转成：`test`，去掉Controller后缀，并转为小写