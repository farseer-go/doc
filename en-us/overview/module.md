# 内置模块
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/fs)

在您使用框架中的各组件时，大部份组件会要求你显示的依赖模块，用于初始化工作：

- cache.module
- cacheMemory.module
- data.module
- elasticSearch.module
- eventBus.module
- fSchedule.module
- linkTrace.module
- queue.module
- rabbit.module
- redis.module
- redisStream.module
- tasks.module
- webapi.module
- etcd.module

## 模块化的意义

主要表现在两个方面：

- 将各个职责划分为不同的模块，以此做到高内聚、低耦合
- init()偏向于隐式调用，并且对初始化工作不够精细

!> 比如A、B两个模块，需要交叉执行某些初始，但又不希望他们有耦合时，特别有用。

## DependsModule 模块依赖

```go
package main

type StartupModule struct{}

func (module StartupModule) DependsModule() []modules.FarseerModule {
	return []modules.FarseerModule{webapi.Module{}, interfaces.Module{}, infrastructure.Module{}}
}

....
```

`DependsModule`函数告诉启动器，需要依赖哪些模块，如：

- webapi.Module （webapi组件模块）
- interfaces.Module（自己项目：接口层模块）
- infrastructure.Module（自己项目：基础设施层模块）

?> 我们依赖的模块，也与`StartupModule`一样，也会有它本身依赖的模块。这是一个递归的过程，直到找到最后一个模块

!> 如果你的`StartupModule`不需要依赖组件，你可以通过`return nil`来返回

## 模块的初始化顺序

1. 按依赖关系，先执行`PreInitialize()`函数
2. 按依赖关系，然后执行`Initialize()`函数
3. 按依赖关系，最后执行`PostInitialize()`函数

?> `StartupModule`模块是最外层的，因此也是最后才会被执行到

!> 这里的执行，是先将所有模块执行完`PreInitialize()`后，才会接着执行`Initialize()`，最后才执行`PostInitialize()`。

## 实际项目中的模块设计

![](https://farseer-go.github.io/doc/images/farseer-go.png)
在这张图中，我们就可以很清晰看到各个模块间的依赖关系，图中使用的是DDD架构。

- startupModule
- infrastructure.Module
- interfaces.Module
- application.Module
- domain.Module

这几个模块，都是在您的应用系统中自己定义的模块。用于显示申明各层之间的依赖关系。

如果您不使用DDD架构，您也可以使用传统的三层结构来定义各层的模块。
