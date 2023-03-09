# Method
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/webapi)

当MVC风格进行注册时，`默认`会将所有的Action采用`POST`的方式进行`注册`。

?> `minimalApi`是单独注册的，所以在注册时就指定了`Method`

## 1、指定Action的Method
在调用`webapi.RegisterController`注册时，通过`Action`字段来指定：
```go
webapi.RegisterController(&testController.TestController{
    BaseController: controller.BaseController{
        Action: map[string]controller.Action{
            "Hello1": {Method: "GET"},
            "Hello2": {Method: "POST", Params: "pageSize,pageIndex"},
        },
    },
})
```
我们可以看到`Action`字段是在`BaseController`这个内嵌字段中的。同时它是一个map类型。

!> 这里的map只在需要的Action才要设置，如果是默认值的情况下，可以不用全部都显示指定。

- `Key`：代表Action Name
- `Value`：对Action的配置

## 2、controller.Action的结构
```go
package controller
type Action struct {
	Method string // POST/GET/PUT/DELETE
	Params string // 函数的入参名称
}
```
通过注释可以看出来，就两个字段：
- `Method`：指定http method
- `Params`：如果Action的参数`不是DTO模式时`，则要`显示传参数的名称`（webapi才能根据这个来自动绑定映射）

?> 通过设置这个对象，可以实现自定义的一些配置。（随着后期webapi的升级，还会加入更多的配置）