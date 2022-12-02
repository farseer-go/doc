# minimalApi 路由

minimalApi的路由非常简单，注册有以下几种：
- webapi.`RegisterPOST`
- webapi.`RegisterGET`
- webapi.`RegisterPUT`
- webapi.`RegisterDELETE`

```go
func RegisterPOST(route string, actionFunc any, params ...string)
```

route参数：即路由url

> 如传入："/projectgroup/tolist"时，则完整URL为：http://localhost:8888/projectgroup/tolist

```go
webapi.RegisterPOST("/mini/hello1", testMiniapi.Hello1)
webapi.RegisterPOST("/projectgroup/tolist", projectGroupApp.ToPageList, "pageSize", "pageIndex")
webapi.Run()
```