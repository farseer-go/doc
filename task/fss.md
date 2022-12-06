# fss 分布式调度中心客户端
> 包：`"github.com/farseer-go/fss"`

> 模块：`fss.Module`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/fss)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/fss)
![](https://img.shields.io/github/v/release/farseer-go/fss)
![](https://img.shields.io/github/languages/code-size/farseer-go/fss)
![](https://img.shields.io/github/directory-file-count/farseer-go/fss)
![](https://img.shields.io/github/last-commit/farseer-go/fss)

## 概述
FSS是一款跨语言分布式的调度中心

- `高可用（HA）`：多实例的job客户端。同一个任务、同一个job实例只会被调度一次 
- `快速搭建`：服务端可运行于docker或k8s下，1分钟即可把服务部署到您的生产环境中 
- `轻量级`：低内存（没有客户端连接的时候130m，有任务的时候250m）、低CPU消耗，依赖少。 
- `动态执行`：可定时、间隔时间、Cron、或由业务方job动态设定下次执行时间。 
- `快速上手`：Farseer.Net.Job（.NET CORE)、`farseer-go.fss(golang)`，可以快速实现一个job 
- `可视化`：使用[FOPS](https://github.com/FarseerNet/fops.go)，可以维护任务组，查看任务进度、耗时、日志。

FSS服务端：
- [https://github.com/FarseerNet/fss.go](https://github.com/FarseerNet/fss.go) （go版本，持续更新中）
- [https://github.com/FarseerNet/fss.net](https://github.com/FarseerNet/fss.net)（.net core版本，不再更新）

?> 这个是FSS服务端的`客户端组件`，使得接入服务端变得非常简单。

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
        flog.Info("doing...")
		return true
	}
}
```

!> 提示：`fss.RegisterJob`也可以放在启动模块去执行。