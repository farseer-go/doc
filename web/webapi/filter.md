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

### 3.1、farseer.yml配置文件
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

### 3.2、登陆接口颁发token
```go
// 登陆
// @post /user/passport/Login
func Login(req request.LoginRequest, accountLoginRepository accountLogin.Repository) response.LoginResponse {
	httpContext := webapi.GetHttpContext()
	login := accountLoginRepository.ToEntityByAccountName(req.LoginName)
	// 验证
	err := login.CheckLogin(req.LoginPwd)
	exception.ThrowWebExceptionError(403, err)
	claims := map[string]any{
		"LoginName":  login.LoginName,
		"ClusterIds": login.ClusterIds.ToString(","),
		"DateTime":   time.Now(),
	}
	domain.SetLoginAccount(claims) // 登陆事件 用到
	token, _ := httpContext.Jwt.Build(claims)
	return response.LoginResponse{LoginName: login.LoginName, Token: token}
}
```


### 3.3、自定义jwt过滤器
```go
// 账号JWT验证 位置：application.Jwt
type Jwt struct {
}

func (receiver Jwt) OnActionExecuting(httpContext *context.HttpContext) {
	traceHand := container.Resolve[trace.IManager]().TraceHand("验证jwt")
	if !httpContext.Jwt.Valid() {
		exception.ThrowWebExceptionf(context.InvalidStatusCode, context.InvalidMessage)
	}

	claims := httpContext.Jwt.GetClaims()

	// 获取登陆账号
	loginName := parse.ToString(claims["LoginName"])
	exception.ThrowWebExceptionBool(len(loginName) == 0, 207, "登录状态失效")

	// 当前登陆账户
	domain.SetLoginAccount(claims)

	traceHand.End(nil)
}

func (receiver Jwt) OnActionExecuted(httpContext *context.HttpContext) {
}
```

### 3.4、接口api附加上jwt过滤器
```go
// @summary 添加应用
// @post add
// @filter application.Jwt
func Add(req request.AddRequest, appsRepository apps.Repository) {
	// 这里是您的接口业务内容....
}
```

> 可以看到注解，使用了：@filter application.Jwt，当token验证失败将返回：403 您没有权限访问