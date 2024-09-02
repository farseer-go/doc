# IFilter过滤器
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/webapi)

区别于全局中间件的设计，过滤器支持针对特定路由的应用。

中间件与过滤器的生命周期为：

> `http` -> `exception` -> `routing` -> `自定义中间件` -> `自定义过滤器（多个）` -> `handleMiddleware`

## 1、过滤器接口
```go
// IFilter 过滤器
type IFilter interface {
	// OnActionExecuting 页面执行前执行
	OnActionExecuting(httpContext *HttpContext)
	// OnActionExecuted 页面执行后执行
	OnActionExecuted(httpContext *HttpContext)
}
```

## 2、自定义过滤器
```go
type demoFilter struct {}
func (receiver demoFilter) OnActionExecuting(httpContext *context.HttpContext) {
// doing
}
func (receiver demoFilter) OnActionExecuted(httpContext *context.HttpContext) {
// doing
}

func main() {
    fs.Initialize[webapi.Module]("demo")
    webapi.RegisterRoutes(webapi.Route{Url: "/hello2", Action: Hello2}.Filter(demoFilter{}).POST())
    webapi.Run(":8090")
}
```

访问"http://127.0.0.1:8090/hello2" 时，便能执行自定义的过滤器

## 3、Jwt过滤器
Jwt过滤器是框架内置的过滤器，用于对前后端分离的项目，做到鉴权认证

使用前需要进行简单的配置：
```yaml
WebApi:
  Url: ":888"
  Jwt:
    Key: "aaaaaaa"                    # 生成token的秘钥
    KeyType: "HS384"                  # 支持 HS256/HS384/HS512，RS256/RS384/RS512，ES256/ES384/ES512，PS256/PS384/PS512，EdDSA
    HeaderName: "Authorization"       # 前端提交Token，存放到header的name
    InvalidStatusCode: 403            # token无效时的状态码
    InvalidMessage: "您没有权限访问"     # token无效时的提示信息
```
```go
    fs.Initialize[webapi.Module]("demo")
    // 颁发Token给到前端
    webapi.RegisterRoutes(webapi.Route{Url: "/jwt/build", Action: func() {
        claims := make(map[string]any)
        claims["farseer-go"] = "v0.8.0"
        webapi.GetHttpContext().Jwt.Build(claims) // 会写到http head中
    }}.POST())

    // 使用Jwt鉴权
    webapi.RegisterRoutes(webapi.Route{Url: "/jwt/validate", Action: func() string {
        return "hello"
    }}.POST().UseJwt())

    webapi.Run()
```

token验证失败将返回：403 您没有权限访问