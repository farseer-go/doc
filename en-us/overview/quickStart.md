# 快速开始
## fs.Initialize 初始化
_main.go_
```go
package main
import "github.com/farseer-go/fs"

func main() {
	fs.Initialize[StartupModule]("your project Name")
}
```
`farseer-go`是真正意义上的模块化框架，因此在使用前，需要先将依赖的模块进行初始化工作。

`fs.Initialize` 会做几件事情：
1. 收集应用的环境信息
2. 从启动模块开始递归加载所有依赖
3. 按依赖关系，执行模块的初始化工作

## 启动模块
_startupModule.go_
```go
package main

import (
	"fops/infrastructure"
	"fops/interfaces"
	"github.com/farseer-go/fs/modules"
	"github.com/farseer-go/webapi"
)

type StartupModule struct { }

func (module StartupModule) DependsModule() []modules.FarseerModule {
	return []modules.FarseerModule{webapi.Module{}, interfaces.Module{}, infrastructure.Module{}}
}

func (module StartupModule) PreInitialize() { }
func (module StartupModule) Initialize() { }
func (module StartupModule) PostInitialize() { }
func (module StartupModule) Shutdown() { }
```

`StartupModule`是你定义的模块之一（你的项目将作为业务模块，加载到框架中）

简单来说，就是告诉框架，你需要加载哪些模块组件（如Redis、数据库ORM、MQ、ElasticSearch等等）

所有的内置模块（包括StartupModule）都将实现`modules.FarseerModule`接口

## 模块化的意义

主要表现在两个方面：
- 将各个职责划分为不同的模块，以此做到高内聚、低耦合
- init()偏向于隐式调用，并且对初始化工作不够精细

!> 比如A、B两个模块，需要交叉执行某些初始，但又不希望他们有耦合时，特别有用。

## DependsModule 模块依赖

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