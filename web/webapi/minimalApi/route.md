# minimalApi 路由
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/webapi)

使用`minimalApi`非常简单，只需要在`web.Run()`执行前调用`webapi.RegisterXXX`，注册有以下几种：
- webapi.`RegisterRoutes`
- webapi.`RegisterPOST`
- webapi.`RegisterGET`
- webapi.`RegisterPUT`
- webapi.`RegisterDELETE`

```go
// 批量注册路由（推荐使用）
func RegisterRoutes(routes ...Route)
// 注册单个路由
func RegisterPOST(route string, actionFunc any, params ...string)
func RegisterGET(route string, actionFunc any, params ...string)
func RegisterPUT(route string, actionFunc any, params ...string)
func RegisterDELETE(route string, actionFunc any, params ...string)
```
- route string：路由url
- actionFunc any：可以传入任意函数，为Api的逻辑。
- params ...string：对入参为多参数时的一种命名补充（详细后面会说到）

**批量注册：**
```go
fs.Initialize[StartupModule]("demo")
webapi.RegisterRoutes(
    webapi.Route{Url: "/mini/hello1", Method: "GET", Action: Hello1},
    webapi.Route{Url: "/mini/hello2", Method: "POST", Action: Hello2},
)
webapi.Run()
```

**POST路由：**
```go
webapi.RegisterPOST("/mini/hello1", Hello1)

func Hello1(req pageSizeRequest) string {
    return fmt.Sprintf("hello world pageSize=%d，pageIndex=%d", req.PageSize, req.PageIndex)
}
```
?> 完整URL为：`POST` http://localhost:8888/mini/hello1

**GET路由：**

```go
webapi.RegisterGET("/projectgroup/tolist", projectGroupApp.ToPageList, "pageSize", "pageIndex")
```

?> 完整URL为：`GET` http://localhost:8888/projectgroup/tolist

## 1、手动指定参数顺序
当Api函数的入参不是DTO模型时，需要显示传入参数的名称，参数的顺序应与Api函数一致

```go
func main() {
	fs.Initialize[webapi.Module]("FOPS")
	webapi.RegisterPOST("/mini/hello3", Hello3, "pageSize", "pageIndex")
	webapi.Run()
}

// 使用基础参数来接收入参
// 返回pageSizeRequest结构（会自动转成json)
func Hello3(pageSize int, pageIndex int) pageSizeRequest {
    return pageSizeRequest{
        PageSize:  pageSize,
        PageIndex: pageIndex,
    }
}

// 也可以定义一个结构，用于接收参数
type pageSizeRequest struct {
    PageSize  int
    PageIndex int
}
```
在Hello3函数中，有2个入参：pageSize、pageIndex，这时需要在注册路由时`显示的指定每个参数的名称`。

!> 这是因为Go在反射时，`无法获取到函数的参数名称`（只能获取到参数类型）。

## 2、批量注册
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
	fs.Initialize[StartupModule]("FSchedule")

	// 批量注册
	webapi.RegisterRoutes(route)
	webapi.RegisterController(&interfaces.TaskController{})

	webapi.UseApiResponse()
	webapi.Run()
}
```
通过：`webapi.RegisterRoutes`函数，它接收一个`[]webapi.Route`数组，将路由批量注册进来。

## 3、路由模板
普通的URL可能是：http://127.0.0.1:8080/api/news?id=xxx ，这种格式让URL变的更长，且不够友好显示

webapi支持占位符的方式来更友好展示URL：http://127.0.0.1:8080/api/news-{id} ，http://127.0.0.1:8080/api/news/{id}

通过`{id}`占位符来指定这是一个入参变量，其中`{}`表示占位符，`id`表示为一个变量

看下例子：
```go
func main() {
    fs.Initialize[StartupModule]("FSchedule")
    
	webapi.RegisterGET("/mini/hello4/{pageSize}-{pageIndex}", Hello4)
	webapi.RegisterPOST("/mini/hello4/{pageSize}/{pageIndex}", Hello4)
    
	webapi.UseApiResponse()
    webapi.Run(":8888")
}

func Hello4(pageSize int, pageIndex int) (int, int) {
    return pageSize, pageIndex
}
```

我们可以这样访问：
```go
http.Get("http://127.0.0.1:8888/mini/hello4/15-1")
http.Post("http://127.0.0.1:8888/mini/hello4/20/1")
```