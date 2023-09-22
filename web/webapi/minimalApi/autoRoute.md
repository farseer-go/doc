# minimalApi 自动路由

借助fsctl工具，可以解放双手，不需要我们再一个个定义路由。

在应用层中，使用注释的方式标记后，在命令行中执行：`fsctl -r` 便能自动生成路由信息。

**应用层代码：**
```go
// Package orderApp
// @area api/1.0
package orderApp

// List 获取订单列表
// repository：订单仓储，webapi自动注入实例
// @get order/{action}
// @filter jwt
// @message 查询成功
func List(pageSize, pageIndex int, repository order.Repository) collections.PageList[order.DomainObject] {
	// 从仓储接口获取数据
	return repository.ToPageList(pageSize, pageIndex)
}

// Count 获取订单数量
// repository：订单仓储，webapi自动注入实例
// @get order/{action}
// @filter jwt
// @message 查询成功
func Count(repository order.Repository) int64 {
	return repository.Count()
}
```
!> 格式必须是：`// @关键词`，在//和@之间，会有一个空格。
## @area：
指定路由的前缀，如果上面的：api/1.0
## @get：
指定为GET请求，后面带的是API地址。 `@get order/{action}`得到的是：
* http://127.0.0.1:xxx/api/1.0/order/List
* http://127.0.0.1:xxx/api/1.0/order/Count

## @filter：
指定要加载的过滤器

## @message：
指定后会修改返回的提示信息：
```go
{
    "Status": true,
    "StatusCode": 200,
    "StatusMessage": "查询成功",
    "Data": { }
}
```