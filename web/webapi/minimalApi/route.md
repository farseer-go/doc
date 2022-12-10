# minimalApi 路由

minimalApi的路由非常简单，注册有以下几种：
- webapi.`RegisterPOST`
- webapi.`RegisterGET`
- webapi.`RegisterPUT`
- webapi.`RegisterDELETE`

```go
// route参数：注册的路由地址
func RegisterPOST(route string, actionFunc any, params ...string)
func RegisterGET(route string, actionFunc any, params ...string)
func RegisterPUT(route string, actionFunc any, params ...string)
func RegisterDELETE(route string, actionFunc any, params ...string)
```

## 注册POST路由
```go
webapi.RegisterPOST("/mini/hello1", testMiniapi.Hello1)
```
完整URL为：`POST` http://localhost:8888/mini/hello1

## 注册GET路由

```go
webapi.RegisterPOST("/projectgroup/tolist", projectGroupApp.ToPageList, "pageSize", "pageIndex")
```

完整URL为：`GET` http://localhost:8888/projectgroup/tolist

## 批量注册
如果要注册的路由太多时，可以采用批量注册的方式，可以将路由单独放到一个`route.go`文件中：

_route.go_
```go
var route = []webapi.Route{
	{Url: "/meta/GetClientList", Method: "POST", Action: clientApp.ToList},
	{Url: "/meta/GetClientCount", Method: "POST", Action: clientApp.GetCount},
	{Url: "/meta/GetRunLogList", Method: "POST", Action: taskLogApp.GetList},
	{Url: "/meta/CopyTaskGroup", Method: "POST", Action: taskGroupApp.CopyTaskGroup},
	{Url: "/meta/DeleteTaskGroup", Method: "POST", Action: taskGroupApp.Delete},
	{Url: "/meta/AddTaskGroup", Method: "POST", Action: taskGroupApp.Add},
}
```

_main.go_
```go
func main() {
	fs.Initialize[StartupModule]("fss")

	// 批量注册
	webapi.RegisterRoutes(route)
	webapi.RegisterController(&interfaces.TaskController{})

	webapi.UseApiResponse()
	webapi.Run()
}
```
通过：`webapi.RegisterRoutes`函数，它接收一个`[]webapi.Route`数组，将路由批量注册进来。