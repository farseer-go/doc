# httpContext 上下文
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/webapi)

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
Header支持隐式绑定（自动绑定到struct），详情看[隐式绑定Header](web/webapi/mvc/bindHeader.md)
### 状态码
如果需要返回自定义的状态码，可以使用WriteCode
```go
r.HttpContext.Response.WriteCode(200)
```