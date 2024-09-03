# websocket 概述
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/webapi)

## 1、介绍
> 得益于`http.Handler`接口的设计思想。可以让http在原基础下直接升级到websocket协议，因此webapi框架也能非常方便的支持websocket协议

只需要在注册路由时，指定Method 为 `"WS"`，webapi便能升级为websocket协议

并且http与ws共用一个端口。不需要为ws另外定义新的port。比如共同使用80、443

## 2、处理函数
> fsctl工具，已支持ws协议的路由了。请升级到最新版本：`v0.14.2`

首先像miniApi风格一样，定义一个应用层函数：
```go
// ./application/resource.go
// 获取宿主资源使用情况
// @ws /host/resource
func ReceiverResource(context *websocket.Context[Request], manager trace.IManager) {
	if context.GetHeader("Token") != "123456" {
        context.Close()
		return
    }
	
	// 循环5次
	for i := 0; i < 5; i++ {
		// 接收消息
		req := context.Receiver()
		flog.Info(req.Type)

		// 发送消息（json）格式
		context.Send(Request{})

		// 发送消息（string）格式
		context.Send("123456")
	}

	// 关闭连接（可以不需要显式调用）
	context.Close()
}
```
在第3行中，`@ws`表明这是一个websocket协议的路由。

websocket在本框架中有如下`约定`：
1. **Method** 为 `ws` （注：在http中为：get、post、delete、put、update等）
2. 函数的**第1个入参**必须为：`*websocket.Context[T]`，T为泛型any类型，用于执行`context.Receiver()`接收消息时，自动json反序列化成T对象
3. 除了第1个入参类型限制，第2个入参起，支持使用在IOC中已注册的接口，实现自动注入
4. 函数不能有出参
5. 函数必须为导出类型

## 3、路由
当我们项目中，定义了这么一个函数后。在项目根目录执行：`fsctl -r`命令，则会自动生成一条对应的路由：
```go
// ./route.go
// 该文件由fsctl route命令自动生成，请不要手动修改此文件
package main

import (
	"github.com/farseer-go/webapi"
	"github.com/farseer-go/webapi/context"
)

var route = []webapi.Route{
    {"WS", "/api/host/resource", application.ReceiverResource, "", []context.IFilter{}, []string{"context", ""}},
}
```
> 关于fsctl工具，请查阅[自动路由](web/webapi/minimalApi/autoRoute)
根据fsctl工具生成规则，会在项目根目录上，生成route.go文件
## 4、main演示
```go
// ./main.go
package main

import (
	"github.com/farseer-go/fs"
	"github.com/farseer-go/webapi"
)

func main() {
	fs.Initialize[StartupModule]("demo")
	webapi.RegisterRoutes(route...)
	webapi.UseCors()        // 使用CORS中间件
	webapi.UseApiResponse() // 让所有的返回值，包含在core.ApiResponse中
	//webapi.PrintRoute()     // 打印所有路由信息到控制台
	webapi.UseApiDoc() // 开启api doc文档
	//webapi.UseValidate() // 使用DTO验证
	//webapi.UseStaticFiles() // 使用静态文件 在根目录./wwwroot中的文件
	webapi.UseHealthCheck() // 开启健康检查
	//webapi.UsePprof()       // 开启pprof
	webapi.Run()                // 运行web服务，端口配置在：farseer.yaml Webapi.Url 配置节点
}
```

可以看到，webapi框架支持的websocket协议也一样是极简的风格，与普通的http协议，没有太大的差别。