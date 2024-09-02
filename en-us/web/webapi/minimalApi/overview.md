# minimalApi 概述
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/webapi)

## 介绍
> minimalApi是一种`极简`、`优雅`的风格，`不需要依赖任何参数`，它看起来就是一个普通的函数

只需要在`webapi.Run()`启动前注册就可以运行了，不需要特意去依赖`http.Request`、`http.Response`，极大简化了应用代码。

_最佳实践_

?> `minimalApi`是作者推荐的方式，因为它不需要依赖`controller.BaseController`，也就不需要专门建一个UI层。是一种`动态api方案`。

```go
func main() {
	fs.Initialize[webapi.Module]("FOPS")
	webapi.RegisterPOST("/mini/hello1", Hello1)
	webapi.Run()
}

// 使用结构（DTO）来接收入参
// 返回string
func Hello1(req pageSizeRequest) string {
	return fmt.Sprintf("hello world pageSize=%d，pageIndex=%d", req.PageSize, req.PageIndex)
}

// 也可以定义一个结构，用于接收参数
type pageSizeRequest struct {
    PageSize  int
    PageIndex int
}
```


## 特殊说明
!> 由于minimalApi不需要依赖controller、httpContext，因此`不支持获取header`，如果需要获取，可以使用`mvc模式`。

?> 在不需要header的场景下，推荐大家用`minimalApi风格`。