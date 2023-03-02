# CORS跨域
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/webapi)

> 得益于中间件，使跨域CORS的支持变的非常简单。

要开启跨域支持，只需要加载CORS中间件即可：

```go
webapi.UseCors() // 开启跨域
webapi.UseApiResponse()
webapi.Run()
```