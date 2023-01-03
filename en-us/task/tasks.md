# tasks 本地调度
> 包：`"github.com/farseer-go/tasks"`

tasks组件可以帮助我们快速编写一个`定时任务`，使用非常简单。

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/tasks)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/tasks)
![](https://img.shields.io/github/v/release/farseer-go/tasks)
![](https://img.shields.io/github/languages/code-size/farseer-go/tasks)
[![Build](https://github.com/farseer-go/tasks/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/tasks/actions/workflows/test.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/tasks)

## tasks.TaskContext 上下文
```go
// 运行任务的上下文
type TaskContext struct { }

// 设置休眠时间
func (receiver *TaskContext) SetNextTime(nextTime time.Time)

// 设置休眠时间
func (receiver *TaskContext) SetNextDuration(d time.Duration)
```
提供了两个方法`SetNextTime`、`SetNextDuration`，他们只是支持的类型不同，`效果是一样的`。

## 1、tasks.Run 运行任务
函数的定义
```go
// 运行一个任务，运行前先休眠
func Run(taskName string, interval time.Duration, taskFn func(context *TaskContext), ctx context.Context)
```
- `taskName`：任务名称
- `interval`：任务运行的间隔时间 
- `taskFn`：要运行的任务回调函数

_演示：_
```go
import "github.com/farseer-go/tasks"
import "github.com/farseer-go/fs/flog"

main () {
    tasks.Run("testRun", 1*time.Second, testRun, context.Background())
    func testRun(context *tasks.TaskContext) {
        flog.Info("doing...")
    }
}
```
## 2、tasks.RunNow 立即运行任务
函数的定义
```go
// 运行一个任务
func RunNow(taskName string, interval time.Duration, taskFn func(context *TaskContext), ctx context.Context)
```
- `taskName`：任务名称
- `interval`：任务运行的间隔时间
- `taskFn`：要运行的任务回调函数

_演示：_
```go
import "github.com/farseer-go/tasks"
import "github.com/farseer-go/fs/flog"

main () {
    tasks.Run("testRun", 1*time.Second, testRun, context.Background())
    func RunNow(context *tasks.TaskContext) {
        flog.Info("doing...")
    }
}
```

## 说明
`tasks.Run`、`tasks.RunNow`都是会执行任务，只不过`tasks.Run`会先`根据interval参数先sleep`，而`tasks.RunNow`会`立即执行`。