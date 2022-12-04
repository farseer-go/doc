# fss 分布式调度中心客户端
> 包：`"github.com/farseer-go/fss"`

> 模块：`fss.Module`

## 配置
_./farseer.yaml_
```yaml
FSS:
  Server: "http://127.0.0.1:888"
  ReservedTaskCount: 20
  PullCount: 6
  WorkCount: 20
  PullInterval: 500
```

- `Server`: FSS服务端地址，多个用`，`分隔
- `ReservedTaskCount`: 20
- `PullCount`: 客户端每次拉取数量
- `WorkCount`: 允许最大并行执行的任务数量
- `PullInterval`: 客户端每次拉取的时间间隔（ms）

## 注册Job
```go
func RegisterJob(jobName string, jobFn jobFunc)

type jobFunc func(context IFssContext) bool
```
参数说明：
- `jobName`：在FSS服务端配置的JobName
- `jobFn`：回调函数，在下发任务后，会执行的函数

## IFssContext上下文
```go
type IFssContext interface {
	// SetProgress 上传进度0-100
	SetProgress(rate int)
	// SetNextAt 本次执行完后，下一次执行的间隔时间
	SetNextAt(d time.Duration)
	// Logger 写入到FSS平台的日志
	Logger(logLevel eumLogLevel.Enum, log string)
	// GetData 获取数据
	GetData(key string) string
	// SetData 获取数据
	SetData(key string, val string)
	// GetTask 获取任务数据
	GetTask() taskVO
}
```
`IFssContext上下文`提供的方法很简单，这里不再描述。


## 完整示例
```go
main () {
    fs.Initialize[fss.Module]("分布式调度中心客户端演示")
    
	// 注册任务
	fss.RegisterJob("ClearHisTask", clearHisTaskJob)
	
	// 任务ClearHisTask的处理逻辑
	func clearHisTaskJob(context fss.IFssContext) bool {
		// doing....
		return true
	}
}
```

!> 提示：`fss.RegisterJob`也可以放在启动模块去执行。