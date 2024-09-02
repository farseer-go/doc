# eventBus 事件总线
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/eventBus)

> 包：`"github.com/farseer-go/eventBus"`
> 
> 模块：`eventBus.Module`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/eventBus)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/eventBus)
![](https://img.shields.io/github/v/release/farseer-go/eventBus)
[![codecov](https://img.shields.io/codecov/c/github/farseer-go/eventBus)](https://codecov.io/gh/farseer-go/eventBus)
![](https://img.shields.io/github/languages/code-size/farseer-go/eventBus)
[![Build](https://github.com/farseer-go/elasticSearch/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/elasticSearch/actions/workflows/build.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/eventBus)

## 1、概述
以事件驱动的方式来解耦业务逻辑，在`DDD`中，事件总线是必然用到的技术。

当两个业务模块相互之间有业务关联，但又不希望在代码结构上直接依赖。

则可以使用事件驱动的方式来解耦相互之间的依赖。

## 2、发布事件
本着farseer-go极简、优雅风格，使用eventBus组件也是非常简单的：

_函数定义_
```go
// 发布事件（同步、阻塞）
func PublishEvent(eventName string, message any)

// 发布事件（异步）
func PublishEventAsync(eventName string, message any)
```
- `eventName`：事件名称
- `message`：事件消息

_演示：_
```go
type newUser struct {
    UserName string
}

func main() {
    fs.Initialize[eventBus.Module]("queue生产消息演示")

    // 同步（阻塞）
    eventBus.PublishEvent("new_user_event", newUser{UserName: "steden"})

    // or 异步（非阻塞）
    eventBus.PublishEventAsync("new_user_event", newUser{UserName: "steden"})
}
```

## 3、订阅事件
_函数定义_
```go
// 订阅
func Subscribe(eventName string, fn core.ConsumerFunc)
// 回调函数
type consumerFunc func(message any, ea EventArgs)
```
- `eventName`：事件名称
- `fn`：事件回调函数
- `message`：事件消息
- `ea`：事件参数

_演示：_
```go
type newUser struct {
    UserName string
}

func main() {
    fs.Initialize[eventBus.Module]("queue生产消息演示")

    eventBus.Subscribe("new_user_event", func (message any, ea core.EventArgs) {
        user := message.(NewUser)
        // do.....
    })
}
```

## 4、使用IOC
在上文中，我们会显示的依赖eventBus包，如果您想使用接口方式，也是支持的：
```go
eventBus.RegisterEvent("TaskScheduler", domainEvent.SchedulerEvent)

// SchedulerEvent 任务调度
func SchedulerEvent(message any, _ core.EventArgs) {
	...
}
```
首先我们在初始化阶段，使用`eventBus.RegisterEvent`注册一个事件，事件名称为：`TaskScheduler`，事件订阅：`domainEvent.SchedulerEvent`

接着可以使用container包来解析接口，并发布事件：
```go
event:= container.Resolve[core.IEvent]("TaskScheduler")
event.Publish(....)
```

同样支持注入的方式：
```go
type TaskGroupMonitor struct {
	SchedulerEventBus core.IEvent `inject:"TaskScheduler"` // 任务调度事件
}

func (receiver *TaskGroupMonitor) waitScheduler() {
    _ = receiver.SchedulerEventBus.Publish(receiver)
}
```

## 5、使用Redis的发布订阅
eventBus包（模块），是在当前进程实现的发布与订阅。如果你希望你的事件能被其它机器上的实例执行。我们可以使用farseer-go/redis包来实现
```go
// 注册客户端更新通知事件
redis.RegisterEvent("default", "ClientUpdate", domainEvent.ClientUpdateEvent)

// ClientUpdateEvent 客户端有更新（Redis订阅）
func ClientUpdateEvent(message any, _ core.EventArgs) {
    var clientDO *client.DomainObject
    _ = json.Unmarshal([]byte(message.(string)), clientDO)
    // doing....
}
```
上面这段是注册事件的实际例子，接下来看下事件的发布：
```go
type clientRepository struct {
	ClientUpdateEventBus core.IEvent `inject:"ClientUpdate"`
}

func (receiver *clientRepository) Save(do *client.DomainObject) {
    // 发到所有节点上
    _ = receiver.ClientUpdateEventBus.Publish(do)
}
```
使用上是一样的，只是实现的对象从eventBus换成了redis。对于事件发布者而言，并不感知是使用哪种实现发布的。

这是因为不管是eventBus还是redis，都共用了core.IEvent这个接口，搭配container(IOC)技术，实现了技术细节的解耦。

?> 使用redis事件，可以实现集群广播效果，一个事件发布后，所有机器都能接收。