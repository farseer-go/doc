# httpContext 上下文
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/webapi)

> 通过上下文，我们可以获取很多http的信息，如header、cookies、session

```go
type HttpContext struct {
    Request          *HttpRequest                                   // Request
    Response         *HttpResponse                                  // Response
    Header           collections.ReadonlyDictionary[string, string] // 头部信息
    Cookie           *HttpCookies                                   // Cookies信息
    Session          *HttpSession                                   // Session信息
    Route            *HttpRoute                                     // 路由信息
    URI              *HttpURL                                       // URL信息
    Data             *HttpData                                      // 用于传递值
    Method           string                                         // 客户端提交时的Method
    ContentLength    int64                                          // 客户端提交时的内容长度
    ContentType      string                                         // 客户端提交时的内容类型
    Exception        any                                            // 是否发生异常
    Close            bool
    TransferEncoding []string
}
```

## 1、MVC模式获取httpContext
```go
type TestHeaderController struct {
    controller.BaseController   // 嵌入BaseController
}
func (r *TestController) Hello3() (TValue string) {
	return r.HttpContext.Header.GetValue("Content-Type")
}
```
通过嵌入BaseController，便可获取HttpContext了
## 2、MinimalApi模式获取httpContext
```go
func Hello10() string {
	return webapi.GetHttpContext().Header.GetValue("Content-Type")
}
```
通过`webapi.GetHttpContext()`函数，可以获取到当前API访问时的HttpContext
> mvc模式也是支持此种方式的

## 3、Header
通过r.HttpContext.Header可以获取到所有Header信息

Header支持隐式绑定（自动绑定到struct），详情看[隐式绑定Header](web/webapi/mvc/bindHeader.md)

## 4、Cookies
通过httpContext可以操作Cookies，Cookies提供了最简单的方法来获取、设置Cookie值
- webapi.GetHttpContext().Cookie.Get *获取Cookie*
- webapi.GetHttpContext().Cookie.GetValue *获取Cookie*
- webapi.GetHttpContext().Cookie.SetValue *设置Cookie*
- webapi.GetHttpContext().Cookie.SetSuretyValue *设置Cookie安全值，将不允许脚本读取该值（HttpOnly）*
- webapi.GetHttpContext().Cookie.SetCookie *设置Cookie*
- webapi.GetHttpContext().Cookie.Remove *删除Cookie*

## 5、Session
通过httpContext可以操作Session，Session提供了最简单的方法来获取、设置Session值
Session支持通过配置的方式来定义Session的存储方式（进程、Redis）、以及保存时间（默认20分钟）
```yaml
WebApi:
  Url: ":888"
  Session:
    Store: "Redis"    # 以redis存储
    StoreConfigName: "default"  # redis的配置名称
    Age: 1800     # session过期时间
```
使用
- webapi.GetHttpContext().Session.GetValue *获取Session*
- webapi.GetHttpContext().Session.SetValue *设置Session*
- webapi.GetHttpContext().Session.Remove *删除Session*
- webapi.GetHttpContext().Session.Clear *清空Session*

!> 注意，Session功能需要手动开启，使用Session中间件来加载：`webapi.UseSession()`

## 6、状态码
如果需要返回自定义的状态码，可以使用WriteCode
```go
func Hello10() string {
    return webapi.GetHttpContext().Response.WriteCode(200)
}
```

## 7、自定义数据
当需要在自定义中间件设置值，在后面环节获取之前设置的值时。可以使用HttpData对象
比如：
```go
func (receiver *Http) Invoke(httpContext *context.HttpContext) {
    httpContext.Data.Set("key1", "val1")
    receiver.IMiddleware.Invoke(httpContext)
}	
```

在应用层获取：
```go
webapi.GetHttpContext().Data.Get("key1")
```