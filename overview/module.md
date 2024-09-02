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

## 1、模块化的意义

主要表现在两个方面：

- 将各个职责划分为不同的模块，以此做到高内聚、低耦合
- init()偏向于隐式调用，并且对初始化工作不够精细

!> 比如A、B两个模块，需要交叉执行某些初始，但又不希望他们有耦合时，特别有用。

## 2、如何定义一个模块
只需要实现`modules.FarseerModule`接口，就是一个标准的模块
```go
package modules

// FarseerModule 依赖的模块
type FarseerModule interface {
	// DependsModule 依赖的模块
	DependsModule() []FarseerModule
}
```
第3节提到的`StartupModule`就是一个标准模块

## 3、DependsModule 模块依赖

```go
package main

type StartupModule struct{}

func (module StartupModule) DependsModule() []modules.FarseerModule {
	return []modules.FarseerModule{webapi.Module{}, interfaces.Module{}, infrastructure.Module{}}
}
```

`DependsModule`函数告诉启动器，需要依赖哪些模块，如：

- webapi.Module （webapi组件模块）
- interfaces.Module（自己项目：接口层模块）
- infrastructure.Module（自己项目：基础设施层模块）

?> 我们依赖的模块，也与`StartupModule`一样，也会有它本身依赖的模块。这是一个递归的过程，直到找到最后一个模块

!> 如果你的`StartupModule`不需要依赖组件，你可以通过`return nil`来返回

## 4、项目中模块的设计

![](https://farseer-go.github.io/doc/images/farseer-go.png)
在这张图中，我们就可以很清晰看到各个模块间的依赖关系，图中使用的是DDD架构。

- startupModule
- infrastructure.Module
- interfaces.Module
- application.Module
- domain.Module

这几个模块，都是在您的应用系统中自己定义的模块。用于显示申明各层之间的依赖关系。

如果您不使用DDD架构，您也可以使用传统的三层结构来定义各层的模块。

## 5、模块接口
```go
package modules

// FarseerModule 依赖的模块
type FarseerModule interface {
	// DependsModule 依赖的模块
	DependsModule() []FarseerModule
}

// FarseerPreInitializeModule 预初始化
type FarseerPreInitializeModule interface {
	FarseerModule
	// PreInitialize 预初始化
	PreInitialize()
}

// FarseerInitializeModule 初始化
type FarseerInitializeModule interface {
	FarseerModule
	// Initialize 初始化
	Initialize()
}

// FarseerPostInitializeModule 初始化之后
type FarseerPostInitializeModule interface {
	FarseerModule
	// PostInitialize 初始化之后
	PostInitialize()
}

// FarseerShutdownModule 应用关闭之前先关闭模块
type FarseerShutdownModule interface {
	FarseerModule
	// Shutdown 应用关闭之前先关闭模块
	Shutdown()
}
```
这是一个完整的模块接口定义，你定义的模块实现这些接口，则会在框架启动时被执行到。

## 6、模块的初始化顺序

1. 按依赖关系，先执行`PreInitialize()`函数
2. 按依赖关系，然后执行`Initialize()`函数
3. 按依赖关系，最后执行`PostInitialize()`函数

?> `StartupModule`模块是最外层的，因此也是最后才会被执行到

!> 这里的执行，是先将所有模块执行完`PreInitialize()`后，才会接着执行`Initialize()`，最后才执行`PostInitialize()`。

## 7、演示
演示代码，请参考：[小型电商网站](https://github.com/farseer-go/demo/tree/main/shopping)

`StartupModule`：启动模块（可以看到依赖了：`infrastructure.Module`, `interfaces.Module`）
```go
package main
import (
	"github.com/farseer-go/fs/modules"
	"shopping/infrastructure"
	"shopping/interfaces"
)
type StartupModule struct { }
func (module StartupModule) DependsModule() []modules.FarseerModule {
    return []modules.FarseerModule{infrastructure.Module{}, interfaces.Module{}}
}
```
`infrastructure.Module`：基础设施层模块（可以看到依赖了：`redis.Module`, `data.Module`，`eventBus.Module`，`queue.Module`）
```go
package infrastructure

import (
	"github.com/farseer-go/data"
	"github.com/farseer-go/eventBus"
	"github.com/farseer-go/fs/container"
	"github.com/farseer-go/fs/modules"
	"github.com/farseer-go/queue"
	"github.com/farseer-go/redis"
	"shopping/domain/procate"
	"shopping/domain/product"
	"shopping/infrastructure/repository"
)

type Module struct { }

func (module Module) DependsModule() []modules.FarseerModule {
	// 使用到了redis模块、data(orm)模块、eventBus（事件总线）模块、queue（本地队列）模块
	// 这些模块都是farseer-go内置的模块
	return []modules.FarseerModule{redis.Module{}, data.Module{}, eventBus.Module{}, queue.Module{}}
}

func (module Module) PostInitialize() {
	// 初始化商品分类仓储
	repository.InitProCate()
	// 初始化商品仓储
	repository.InitProduct()
	// 初始化商品仓储
	repository.InitStock()
	// 初始化订单仓储
	repository.InitOrder()

	// 为了方便演示，自动初始化商品分类
	procateRepository := container.Resolve[procate.Repository]()
	if procateRepository.Count() == 0 {
		procateRepository.Add(procate.DomainObject{Id: 1, Name: "苹果"})
		procateRepository.Add(procate.DomainObject{Id: 2, Name: "科沃斯"})
		procateRepository.Add(procate.DomainObject{Id: 3, Name: "游戏机"})
		procateRepository.Add(procate.DomainObject{Id: 4, Name: "电饭煲"})
		procateRepository.Add(procate.DomainObject{Id: 5, Name: "Bose"})
		procateRepository.Add(procate.DomainObject{Id: 6, Name: "戴森"})
	}

	// 为了方便演示，自动初始化10条商品记录
	productRepository := container.Resolve[product.Repository]()
	if productRepository.Count() == 0 {
		productRepository.Add(product.DomainObject{Cate: product.CateEO{Id: 1, Name: "苹果"}, Stock: 100, Price: 8999, Caption: "Apple iPhone 14 Pro Max", Desc: "Apple iPhone 14 Pro Max (A2896) 512GB 银色 支持移动联通电信5G 双卡双待手机", ImgSrc: "https://img10.360buyimg.com/n1/s450x450_jfs/t1/52631/5/21888/20074/63191b75E9234fd5f/1ce070cb00b6f896.jpg.avif"})
		productRepository.Add(product.DomainObject{Cate: product.CateEO{Id: 6, Name: "戴森"}, Stock: 100, Price: 4500, Caption: "戴森吸尘器V12", Desc: "戴森（DYSON）V12 Detect Slim Fluffy轻量手持吸尘器大吸力", ImgSrc: "https://img13.360buyimg.com/n1/jfs/t1/19688/36/16587/116628/6392d71cEaf3517f1/f8540270aa837711.jpg.avif"})
		productRepository.Add(product.DomainObject{Cate: product.CateEO{Id: 3, Name: "游戏机"}, Stock: 100, Price: 4300, Caption: "微软Xbox Series主机", Desc: "微软（Microsoft） 【国内保税仓】微软Xbox Series主机 次时代家用4K高清游戏主机 Xbox Series X日版", ImgSrc: "https://img11.360buyimg.com/n1/jfs/t1/148482/19/14661/44451/5fb37e27Ed4e29739/b803e7e6e730458f.jpg"})
		productRepository.Add(product.DomainObject{Cate: product.CateEO{Id: 2, Name: "科沃斯"}, Stock: 100, Price: 4600, Caption: "科沃斯扫地机器人 T10", Desc: "科沃斯扫地机器人 T10 OMNI扫拖一体机 吸拖洗烘一体拖地机器人洗地机擦地机 智能全自动集尘清洗", ImgSrc: "https://img14.360buyimg.com/n1/jfs/t1/221921/29/21654/66050/6343dbe6E65851991/26b01cc077765280.jpg.avif"})
		productRepository.Add(product.DomainObject{Cate: product.CateEO{Id: 4, Name: "电饭煲"}, Stock: 100, Price: 4999, Caption: "福库电饭煲", Desc: "韩国原装进口电饭锅3 IH加热高压无压可切换家用智能多功能双压电饭煲1-4人份", ImgSrc: "https://img12.360buyimg.com/n1/jfs/t1/220183/21/12895/369141/6214afa9Ed1efc3de/4c234fe4872bfe1b.jpg.avif"})
		productRepository.Add(product.DomainObject{Cate: product.CateEO{Id: 5, Name: "Bose"}, Stock: 100, Price: 43000, Caption: "Bose LifeStyle 650 家庭影院5", Desc: "Bose LifeStyle 650 博士电视音响 家庭影院5.1 回音壁 客厅 多功能 boss 黑色", ImgSrc: "https://img10.360buyimg.com/n1/jfs/t1/194207/27/22333/61466/6264f81aE57fd1558/fe3b5d2006e6f144.jpg.avif"})
		productRepository.Add(product.DomainObject{Cate: product.CateEO{Id: 5, Name: "Bose"}, Stock: 100, Price: 2999, Caption: "Bose 700 无线消噪耳机-银色", Desc: "Bose 700 无线消噪耳机-银色 手势触控蓝牙降噪耳机 主动降噪头戴式耳机 长久续航", ImgSrc: "https://img11.360buyimg.com/n1/s450x450_jfs/t1/9392/40/17588/329181/62a867caE1ac9df9c/076838c67d546f0f.jpg.avif"})
		productRepository.Add(product.DomainObject{Cate: product.CateEO{Id: 1, Name: "苹果"}, Stock: 100, Price: 14999, Caption: "Apple MacBook Pro 14英寸", Desc: "8核中央处理器 14核图形处理器 16G 256G 深空灰 笔记本", ImgSrc: "https://img14.360buyimg.com/n1/s450x450_jfs/t1/197505/24/13599/83205/616dc631E854d2563/e98b96e5044af9da.jpg.avif"})
		productRepository.Add(product.DomainObject{Cate: product.CateEO{Id: 1, Name: "苹果"}, Stock: 100, Price: 1400, Caption: "Apple苹果 Apple TV 7代 (2022款) ", Desc: "Apple苹果 Apple TV 7代 (2022款) 128GB WIFI+Ethernet版 A15仿生", ImgSrc: "https://img12.360buyimg.com/n1/jfs/t1/59455/13/22349/10684/6355f732E5940e6fe/edd0f8570d454be2.jpg"})
		productRepository.Add(product.DomainObject{Cate: product.CateEO{Id: 6, Name: "戴森"}, Stock: 100, Price: 2999, Caption: "戴森吹风机 HD08 长春花蓝礼盒款", Desc: "戴森(Dyson) 吹风机 Dyson Supersonic 电吹风 负离子 进口家用 礼物推荐 HD08 长春花蓝礼盒款", ImgSrc: "https://img14.360buyimg.com/n1/jfs/t1/126497/34/30895/26643/63490c3dEbc9de636/9cd45e32ca3b1e05.jpg"})
	}
}
```