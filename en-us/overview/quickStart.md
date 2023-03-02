# 快速开始
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/fs)

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

`StartupModule`是你定义的模块之一（你的项目将作为业务模块，加载到框架中）

## 启动模块

_startupModule.go_

```go
package main

type StartupModule struct{}

func (module StartupModule) DependsModule() []modules.FarseerModule {
	return []modules.FarseerModule{webapi.Module{}, interfaces.Module{}, infrastructure.Module{}}
}

func (module StartupModule) PreInitialize() { }
func (module StartupModule) Initialize() { }
func (module StartupModule) PostInitialize() { }
func (module StartupModule) Shutdown() { }
```

简单来说，就是告诉框架，你需要加载哪些模块组件（如Redis、数据库ORM、MQ、ElasticSearch等等）

所有的内置模块（包括StartupModule）都将实现`modules.FarseerModule`接口