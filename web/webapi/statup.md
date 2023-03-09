# webapi 启动
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/webapi)

> 包：`"github.com/farseer-go/webapi"`
> 
> 模块：`webapi.Module`

## 1、启动Web服务
_main.go 入口_
```go
package main
import (
	"github.com/farseer-go/fs"
	"github.com/farseer-go/webapi"
)

func main() {
	fs.Initialize[StartupModule]("FOPS")
	webapi.RegisterPOST("/mini/hello1", Hello1)
	webapi.Run()
}

func Hello1(req pageSizeRequest) string {
	return fmt.Sprintf("hello world pageSize=%d，pageIndex=%d", req.PageSize, req.PageIndex)
}

type pageSizeRequest struct {
	PageSize  int
	PageIndex int
}
```

执行`webapi.Run()`后，便可启动web api服务。

_startupModule.go 启动模块_
```go
package main
import (
    "github.com/farseer-go/fs/modules"
    "github.com/farseer-go/webapi"
)
type StartupModule struct { }

func (module StartupModule) DependsModule() []modules.FarseerModule {
	return []modules.FarseerModule{webapi.Module{}}
}
```
!> 在DependsModule中，依赖了`webapi.Module`模块。

_运行结果：_

```
2022-12-01 17:07:24 基础组件初始化完成
2022-12-01 17:07:24 初始化完毕，共耗时：1 ms 
2022-12-01 17:07:24 ---------------------------------------
2022-12-01 17:07:24 [Info] Web服务已启动：http://localhost:8888/
```

> 在Hello1函数中，`不需要依赖任何框架的参数`，这是`webapi的特色`之一：`极简`、`优雅`
> 
> 我们甚至不需要`接口层（UI层）`，可以直接通过路由指向`逻辑层（或应用层）`

## 2、配置端口

在不做任何配置时，EndPort默认为：`localhost:8888`

?> 配置EndPort的两种方式：

### 2.1、参数配置
```go
func Run(params ...string)
```
params实际只支持一个入参，支持的格式为:
- webapi.Run()
- webapi.Run(`":80"`)
- webapi.Run(`"127.0.0.1:80"`)

### 2.2、配置文件
_./farseer.yaml_
```yaml
WebApi:
  Url: ":8888"
```
?> 支持的格式与参数配置是一样的。

### 2.3、端口查找规则
1. 优先读取通过webapi.Run("xxx")传入的实参
2. 如果未传、或传的是""，则查找配置文件
3. 如果配置文件没有，则使用默认端口：`localhost:8888`