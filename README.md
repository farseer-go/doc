# 概述
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/fs)

![stars](https://img.shields.io/github/stars/farseer-go?style=social)
![license](https://img.shields.io/github/license/farseer-go/fs)
![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/fs)
![release](https://img.shields.io/github/v/release/farseer-go/fs)
[![Build](https://github.com/farseer-go/fs/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/fs/actions/workflows/test.yml)
[![codecov](https://img.shields.io/codecov/c/github/farseer-go/fs)](https://codecov.io/gh/farseer-go/fs)
![badge](https://goreportcard.com/badge/github.com/farseer-go/fs)

## 1、介绍

基于Golang模块化完整的基础设施框架，创建现代化Web应用和APIs

使用目前最为流行的组件（共17个组件），并用模块化技术来使用这些组件。

框架完美支持 `DDD领域驱动` 的战术设计，如`仓储资源库`、`应用层事务`、`领域事件`、`应用层动态WebAPI`。

它有一个[.net core](https://github.com/FarseerNet/Farseer.Net/) 成熟版本，已经使用了10多年，非常棒

![](https://farseer-go.github.io/doc/images/farseer-go.png)

?> 不用担心框架会让你依赖过多的包，farseer-go的组件都是独立的包，不使用的包不会下载到您的应用程序中

## 2、有什么特点？

- `统一配置`：所有的配置被整合到`./farseer.yaml`

- `优雅`：所有的模块都遵循开发者体验优先为原则。

- `模块化`：完整的模块化系统，使你能够开发可重复使用的应用程序模块。

- `领域驱动`：帮助你实现基于DDD的分层架构并构建可维护的代码库。

- `健康检查`：应用启动后会定向FOPS中心上报当前应用的健康状态。并在[fops](https://github.com/farseers/fops)中提供查询界面。

- `分布式日志`：通过flog打印日志，你除了在控制台可以看到，也可以在[fops](https://github.com/farseers/fops)中查询。

- `链路追踪`：隐式为您实现链路追踪，并提供API请求日志、慢查询，并在[fops](https://github.com/farseers/fops)中提供查询界面。

!> FOPS是专为farseer-go框架打造的一款运维平台。支持：链路查询、慢SQL、应用日志、健康检查、应用部署（Docker swarm）等能力。

## 3、集成的组件

| 包名                                                           | 描述          |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
|--------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [fs](https://github.com/farseer-go/fs)                       | 基础核心包       | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/fs)![release](https://img.shields.io/github/v/release/farseer-go/fs)[![Build](https://github.com/farseer-go/fs/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/fs/actions/workflows/test.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/fs)](https://codecov.io/gh/farseer-go/fs)![badge](https://goreportcard.com/badge/github.com/farseer-go/fs)                                                                                |
| [collections](https://github.com/farseer-go/collections)     | 数据集合        | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/collections)![release](https://img.shields.io/github/v/release/farseer-go/collections)[![Build](https://github.com/farseer-go/collections/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/collections/actions/workflows/test.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/collections)](https://codecov.io/gh/farseer-go/collections)![badge](https://goreportcard.com/badge/github.com/farseer-go/collections)                 |
| [webapi](https://github.com/farseer-go/webapi)               | web api服务   | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/webapi)![release](https://img.shields.io/github/v/release/farseer-go/webapi)[![Build](https://github.com/farseer-go/webapi/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/webapi/actions/workflows/test.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/webapi)](https://codecov.io/gh/farseer-go/webapi)![badge](https://goreportcard.com/badge/github.com/farseer-go/webapi)                                                    |
| [mapper](https://github.com/farseer-go/mapper)               | 对象转换        | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/mapper)![release](https://img.shields.io/github/v/release/farseer-go/mapper)[![Build](https://github.com/farseer-go/mapper/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/mapper/actions/workflows/test.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/mapper)](https://codecov.io/gh/farseer-go/mapper)![badge](https://goreportcard.com/badge/github.com/farseer-go/mapper)                                                    | 
| [cacheMemory](https://github.com/farseer-go/cacheMemory)     | 本地缓存        | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/cacheMemory)![release](https://img.shields.io/github/v/release/farseer-go/cacheMemory)[![Build](https://github.com/farseer-go/cacheMemory/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/cacheMemory/actions/workflows/test.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/cacheMemory)](https://codecov.io/gh/farseer-go/cacheMemory)![badge](https://goreportcard.com/badge/github.com/farseer-go/cacheMemory)                 |
| [redis](https://github.com/farseer-go/redis)                 | client      | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/redis)![release](https://img.shields.io/github/v/release/farseer-go/redis)[![Build](https://github.com/farseer-go/redis/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/redis/actions/workflows/build.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/redis)](https://codecov.io/gh/farseer-go/redis)![badge](https://goreportcard.com/badge/github.com/farseer-go/redis)                                                         |
| [data](https://github.com/farseer-go/data)                   | 数据库ORM      | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/data)![release](https://img.shields.io/github/v/release/farseer-go/data)[![Build](https://github.com/farseer-go/data/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/data/actions/workflows/build.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/data)](https://codecov.io/gh/farseer-go/data)![badge](https://goreportcard.com/badge/github.com/farseer-go/data)                                                                | 
| [elasticSearch](https://github.com/farseer-go/elasticSearch) | client      | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/elasticSearch)![release](https://img.shields.io/github/v/release/farseer-go/elasticSearch)[![Build](https://github.com/farseer-go/elasticSearch/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/elasticSearch/actions/workflows/build.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/elasticSearch)](https://codecov.io/gh/farseer-go/elasticSearch)![badge](https://goreportcard.com/badge/github.com/farseer-go/elasticSearch) | 
| [eventBus](https://github.com/farseer-go/eventBus)           | 事件总线        | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/eventBus)![release](https://img.shields.io/github/v/release/farseer-go/eventBus)[![Build](https://github.com/farseer-go/eventBus/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/eventBus/actions/workflows/test.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/eventBus)](https://codecov.io/gh/farseer-go/eventBus)![badge](https://goreportcard.com/badge/github.com/farseer-go/eventBus)                                      | 
| [queue](https://github.com/farseer-go/queue)                 | 本地队列        | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/queue)![release](https://img.shields.io/github/v/release/farseer-go/queue)[![Build](https://github.com/farseer-go/queue/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/queue/actions/workflows/test.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/queue)](https://codecov.io/gh/farseer-go/queue)![badge](https://goreportcard.com/badge/github.com/farseer-go/queue)                                                           | 
| [tasks](https://github.com/farseer-go/tasks)                 | 本地任务        | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/tasks)![release](https://img.shields.io/github/v/release/farseer-go/tasks)[![Build](https://github.com/farseer-go/tasks/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/tasks/actions/workflows/test.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/tasks)](https://codecov.io/gh/farseer-go/tasks)![badge](https://goreportcard.com/badge/github.com/farseer-go/tasks)                                                           | 
| [fSchedule](https://github.com/farseer-go/fSchedule)         | 分布试调度client | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/fSchedule)![release](https://img.shields.io/github/v/release/farseer-go/fSchedule)[![Build](https://github.com/farseer-go/fSchedule/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/fSchedule/actions/workflows/build.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/fSchedule)](https://codecov.io/gh/farseer-go/fSchedule)![badge](https://goreportcard.com/badge/github.com/farseer-go/fSchedule)                             | 
| [utils](https://github.com/farseer-go/utils)                 | 工具集         | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/utils)![release](https://img.shields.io/github/v/release/farseer-go/utils)[![Build](https://github.com/farseer-go/utils/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/utils/actions/workflows/test.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/utils)](https://codecov.io/gh/farseer-go/utils)![badge](https://goreportcard.com/badge/github.com/farseer-go/utils)                                                           |
| [rabbit](https://github.com/farseer-go/rabbit)               | client      | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/rabbit)![release](https://img.shields.io/github/v/release/farseer-go/rabbit)[![Build](https://github.com/farseer-go/rabbit/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/rabbit/actions/workflows/build.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/rabbit)](https://codecov.io/gh/farseer-go/rabbit)![badge](https://goreportcard.com/badge/github.com/farseer-go/rabbit)                                                  |
| [etcd](https://github.com/farseer-go/etcd)                   | client      | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/etcd)![release](https://img.shields.io/github/v/release/farseer-go/etcd)[![Build](https://github.com/farseer-go/etcd/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/etcd/actions/workflows/build.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/etcd)](https://codecov.io/gh/farseer-go/etcd)![badge](https://goreportcard.com/badge/github.com/farseer-go/etcd)                                                                |
| [linkTrace](https://github.com/farseer-go/linkTrace)         | 链路追踪        | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/linkTrace)![release](https://img.shields.io/github/v/release/farseer-go/linkTrace)[![Build](https://github.com/farseer-go/linkTrace/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/linkTrace/actions/workflows/build.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/linkTrace)](https://codecov.io/gh/farseer-go/linkTrace)![badge](https://goreportcard.com/badge/github.com/farseer-go/linkTrace)                             |
| [monitor](https://github.com/farseer-go/redisStream)     | 核心数据上报FOPS  | ![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/monitor)![release](https://img.shields.io/github/v/release/farseer-go/monitor)[![Build](https://github.com/farseer-go/monitor/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/monitor/actions/workflows/build.yml)[![codecov](https://img.shields.io/codecov/c/github/farseer-go/monitor)](https://codecov.io/gh/farseer-go/monitor)![badge](https://goreportcard.com/badge/github.com/farseer-go/monitor)                             |

## 4、如何开始

_main.go_
```go
package main
import "github.com/farseer-go/fs"
import "github.com/farseer-go/fs/modules"
import "github.com/farseer-go/webapi"

func main() {
	fs.Initialize[StartupModule]("your project Name")
}

// StartupModule 启动模块
type StartupModule struct { }

// DependsModule 依赖模块
func (module StartupModule) DependsModule() []modules.FarseerModule {
  return []modules.FarseerModule{webapi.Module{}} // 加载webapi.Module模块
}
func (module StartupModule) PreInitialize() { }
func (module StartupModule) Initialize() { }
func (module StartupModule) PostInitialize() { }
func (module StartupModule) Shutdown() { }
```

?> 在main函数第一行，执行`fs.Initialize`，开始初始化框架

运行后控制台打印加载信息：

```
2023-01-05 16:15:00 AppName：  your project Name
2023-01-05 16:15:00 AppID：    199530571963039744
2023-01-05 16:15:00 AppIP：    192.168.3.55
2023-01-05 16:15:00 HostName： stedenMacBook-Pro.local
2023-01-05 16:15:00 HostTime： 2023-01-05 16:15:00
2023-01-05 16:15:00 PID：      22131

2023-01-05 16:15:00 ---------------------------------------
2023-01-05 16:15:00 Loading Module：webapi.Module
2023-01-05 16:15:00 Loading Module：main.StartupModule
2023-01-05 16:15:00 Loaded, 11 modules in total
2023-01-05 16:15:00 ---------------------------------------
2023-01-05 16:15:00 Elapsed time：0 ms modules.FarseerKernelModule.PreInitialize()
2023-01-05 16:15:00 Elapsed time：0 ms webapi.Module.PreInitialize()
2023-01-05 16:15:00 Elapsed time：0 ms main.StartupModule.PreInitialize()
2023-01-05 16:15:00 ---------------------------------------
2023-01-05 16:15:00 Elapsed time：0 ms webapi.Module.Initialize()
2023-01-05 16:15:00 Elapsed time：0 ms main.StartupModule.Initialize()
2023-01-05 16:15:00 ---------------------------------------
2023-01-05 16:15:00 Elapsed time：0 ms webapi.Module.PostInitialize()
2023-01-05 16:15:00 Elapsed time：0 ms main.StartupModule.PostInitialize()
2023-01-05 16:15:00 ---------------------------------------
2023-01-05 16:15:00 Initialization completed, total time：0 ms 
2023-01-05 16:15:00 [Info] Web service is started：http://localhost:8888/
```

## 4、farseer-go框架演示
我们提供了模拟一个[小型电商网站](https://github.com/farseer-go/demo/tree/main/shopping)，这个DEMO用到的技术有：
* ddd：使用领域驱动设计
* ioc：使用ioc/container，做解耦、注入、依赖倒置
* webapi：api服务，并使用动态api技术
* data：数据库操作
* redis：redis操作
* eventBus：事件驱动
您可以把它下载到本地并运行起来

## 6、Stargazers

[![Stargazers repo roster for @farseer-go/fs](https://reporoster.com/stars/farseer-go/fs)](https://github.com/farseer-go/fs/stargazers)