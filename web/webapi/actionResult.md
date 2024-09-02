# ActionResult 概述
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/webapi)

> 包：`"github.com/farseer-go/webapi/action"`

ActionResult是执行结果的一种抽象封装，目的是让我们可以`输出更多类型`的内容。

在前面的文档说明中，都是基于api输出的。有时候我们还是需要做一些web html的页面，则可以使用`action.IResult`类型来输出。

_比如：_
```go
func main() {
	fs.Initialize[webapi.Module]("action演示")
	webapi.RegisterPOST("/login", login, "username", "password")
    webapi.UseCors()        // 使用CORS中间件
    webapi.PrintRoute()     // 打印所有路由信息到控制台
    webapi.UseApiDoc()      // 开启api doc文档
    webapi.UseValidate()    // 使用DTO验证
    webapi.UseStaticFiles() // 使用静态文件 在根目录./wwwroot中的文件
	webapi.Run()
}

func login(username, password) action.IResult {
	if username == "root" && password == "123456" {
		return action.View()
	}
	return action.Redirect("/")
}
```

当用户名密码正确时，调用`action.View()`，而错误时：`action.Redirect("/")`，即可以实现页面输出，又可以做跳转操作。这就是`action.IResult`带来的好处。

`action.IResult`只是抽像出来的一个接口，这里讲解下每个实现类的功能。

## 1、Redirect 302重定向
跳转到指定的url上
```go
action.Redirect("/")
```
## 2、View 返回视图
`默认视图`
```go
return action.View()
```
当不传入参数时，则以默认视图的方式呈现。查找默认视图的规则是：

?> ./views/`[route][.html]`

?>  示例的地址是：`./views/login.html`

route是在注册该函数时指定的位置，如：
```go
webapi.RegisterPOST("/login", login, "username", "password")
```
那么这里的`route = "/login"`。

而`.html`也是自动附加的，但如果路径的最后包含`.`时，则不会再附加`.html`

`指定视图`
```go
return action.View("/demo.xml")
```
?>  指定后查找的位置为：`./views/demo.xml`

!> 路径中存在`.`，所以不再附加`.html`后缀
## 3、Content 直接输出字符串内容
```go
return Content("aaaa")
```
相当于：`return "aaaa"` 的效果。
## 4、FileContent 输出指定文件径路的内容
```go
return FileContent("/farseer.yaml")
```
比如这里，我们把根目录的配置文件的内容输出到网页中