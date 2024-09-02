# Area 区域
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/webapi)

Area表示可以给注册的Api设置区域（或前缀），比如：
```go
webapi.RegisterPOST("/mini/hello1", testMiniapi.Hello1)
webapi.Area("/api/1.0/", func() {
    webapi.RegisterPOST("/projectgroup/tolist", projectGroupApp.ToPageList, "pageSize", "pageIndex")
})
webapi.Area("/api/2.0/", func() {
    webapi.RegisterController(&TestController{}) // 注册控制器
})
webapi.Run()
```
示例中，我们注册了两个Api，其完整的路由地址是：
- http://localhost:8888/mini/hello1
- http://localhost:8888/api/1.0/projectgroup/tolist
- http://localhost:8888/api/1.0/test/hello1
- http://localhost:8888/api/1.0/test/hello2
- 
可以看出，经过`webapi.Area`注册的路由。会有`/api/1.0/`、`/api/2.0/`的前缀。

在我们需要`区分api版本号`时，特别有用。

?> 当然，你也可以不加版本号，只是为了增加`/api/`前缀。