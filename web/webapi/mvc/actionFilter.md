# IActionFilter过虑器
提供了在执行controller/action前后对请求/响应修改的能力，可以应用于action和控制器上，作用在控制器上将自动被应用到该控制器下所有的action.

IActionFilter过虑器使用的是`AOP的设计模式`，允许我们对Action做`切面编程`。

常见的应用场景例如： 用户验证授权、日志记录、异常错误处理等 。

先看下IActionFilter过虑器的定义：
```go
package controller
// IActionFilter 过滤器
type IActionFilter interface {
	// OnActionExecuting Action执行前
	OnActionExecuting()
	// OnActionExecuted Action执行后
	OnActionExecuted()
}
```
只要在控制器中，实现这个接口，就可以启用过虑器功能了：

_taskController.go_
```go
type TaskController struct {
	controller.BaseController
	Client clientApp.DTO `webapi:"header"`
    sw     *stopwatch.Watch
}

func (r *TaskController) OnActionExecuting() {
    r.sw = stopwatch.StartNew()
	
    r.Client.ActivateAt = time.Now()
    clientApp.UpdateClient(r.Client)
}
func (r *TaskController) OnActionExecuted() {
    flog.Info(r.sw.GetMillisecondsText())
}

func (r *TaskController) JobInvoke(dto request.JobInvokeRequest) string {
	return taskGroupApp.JobInvoke(r.Client, dto)
}

func (r TaskController) Pull(dto request.PullRequest) collections.List[request.TaskDTO] {
	return taskGroupApp.Pull(r.Client, dto)
}
```
如示例，如果接收到Pull请求，执行顺序为：

`OnActionExecuting()` -> `Pull(dto request.PullRequest)` -> `OnActionExecuted()`

在示例中`OnActionExecuting()`，做了[stopwatch.Watch](basic/stopwatch.md)初始化，并开始统计时间，接着在`OnActionExecuted()`时，打印时间消耗日志。

可以看出`IActionFilter过虑器`允许我们在`Action执行前、后`，做逻辑处理。