# go客户端

> 包：`"github.com/farseer-go/fSchedule"`
>
> 模块：`fSchedule.Module`

## 1、配置调度中心地址
**./farseer.yaml**
```yaml
FSchedule:
  Debug:
    Enable: false   # 启用调试模式，启用后不会向调度中心注册任务
    JobName1:       # 调试模式下，配置任务的数据
      Key1: "value1"
      Key2: "value2"
    JobName2:
      Key3: "value3"
      Key4: "value4"
  ClientIp: ""      # 调度中心访问本机的IP地址，默认情况下不需要填写
  ClientPort: 8888  # 调度中心访问本机的端口地址，必须填写
  Server:
    Token: ""       # 与调度中心通信的Token（未实现）
    Address:        # 调度中心服务端地址（支持多个）
      - "http://127.0.0.1:8886"
      - "http://127.0.0.1:8887"
```
## 2、定义任务处理函数
```go
func job1(jobContext *fSchedule.JobContext) bool {
	// 可以从调度中心获取到你配置的数据
    yourData := jobContext.Data.GetValue("CustomDataKey")
	// 也可在执行过程中添加、修改，向调度中心发送数据
	jobContext.Data.Add("CustomDataKey", "CustomDataValue")
	
	// 你的业务逻辑
	// doing.....
	
    return true
}
```

**入参**：`fSchedule.JobContext` 任务上下文，包含任务的相关信息，及控制任务下一次执行时间

**出参**：`true`：本次任务执行成功。`false`：执行失败（失败后，服务端会立即重新调度）。

## 3、向调度中心注册任务
```go
// isEnable：任务是否开启
// name：任务组名称（英文）
// caption：任务组标题（任务说明）
// ver：任务组版本，初始版本号必须为1
// cron：任务计划时间（间隔时间）
// startAt：任务开始时间，单位：时间戳，秒（在这个时间之后才会开始）
// job：任务执行的函数本体
func AddJob(isEnable bool, name, caption string, ver int, cron string, startAt int64, job JobFunc)
```

示例：
```go
func (module Module) PostInitialize() {
    fSchedule.AddJob(true, "Hello"+strconv.Itoa(i), "测试HelloJob"+strconv.Itoa(i), 1, "0/1 * * * * ?", 1674571566, job1)
}
```
`fSchedule.AddJob`向服务端注册任务。需要放到模块中`PostInitialize`方法执行（框架启动时执行一次即可）

job1：任务处理函数

任务组版本说明，如果需要向服务端修改任务组属性，则要在原版本号的基础下+1，否则无效。

> 比如原来服务端版本为3，本次想修改caption，则应将ver改为4。这时服务端才会修改，否则忽略。


## 4、完整示例
```go

import (
	"github.com/farseer-go/fSchedule"
	"github.com/farseer-go/fs"
	"github.com/farseer-go/fs/modules"
	"strconv"
	"testing"
	"time"
)

// 启动模块
type startupModule struct {}
func (module startupModule) DependsModule() []modules.FarseerModule {
	return []modules.FarseerModule{fSchedule.Module{}}
}
func (module startupModule) PostInitialize() {
	// 在这里注册任务
    fSchedule.AddJob(true, "Hello1", "测试HelloJob1", 1, "0/1 * * * * ?", 1674571566, job1)
}

// 主函数
func main() {
	fs.Initialize[startupModule]("test fSchedule")
	time.Sleep(300000 * time.Second)
}

// 执行任务
func job1(jobContext *fSchedule.JobContext) bool {
	// db.delete()... 比如清理日志数据
    return true
}
```

## 5、调试模式
在开发过程中，我们需要先做本地JOB调试。可以通过配置的方式开启调试模式。
```yaml
FSchedule:
  Debug:
    Enable: true   # 启用调试模式，启用后不会向调度中心注册任务
    JobName1:       # 调试模式下，配置任务的数据
      Key1: "value1"
      Key2: "value2"
    JobName2:
      Key3: "value3"
      Key4: "value4"
```
只需要将Enable设为true，将开启调试模式，`调试状态下，不开启与调度中心的通信`。