# fsctl工具

## 1、概述
fsctl的仓库在：[github](https://github.com/farseer-go/fsctl)
为webapi组件自动生成路由信息，省去手动配置路由的繁琐工作

## 2、安装
```shell
sudo curl -L -o /usr/local/bin/fsctl "https://github.com/farseer-go/fsctl/releases/download/v0.17.1/fsctl.$(uname -s).$(uname -m)" && sudo chmod +x /usr/local/bin/fsctl
```

## 3、使用
在你的应用层代码中定义注释信息，fsctl工具会根据这些信息来生成路由
```go
// Buy 购买商品
// @post product/{action}
// @filter Jwt Auth
// @di transaction default
// @di buyOrderEvent buyOrder
// @message 下单成功
func Buy(productId int64, productRepository product.Repository, stockRepository stock.Repository, transaction core.ITransaction, buyOrderEvent core.IEvent) {
	// 减库存，剩余库存>0 ，扣减成功
	stockVal := stockRepository.Set(productId, -1)
	if stockVal > -1 {
		// 把商品信息查出来
		productDO := productRepository.ToEntity(productId)
		// 开启数据库事务
		transaction.Transaction(func() {
			// 发布下单事件
			_ = buyOrderEvent.Publish(&productDO)
		})
	}
}
```
在上面的注释中，以`// @` 开头的信息则为fsctl工具所需的配置。

## 3.1、@post product/{action}
> 定义api以哪种method访问。格式：// @[methodName] api地址，如果api地址包含{action}，则自动使用当前函数名称替换
- // @post
- // @get
- // @delete
- // @put
- // @ws
ws则启动websocket协议，具体的使用，请[参考这里](https://farseer-go.github.io/doc/#/web/webapi/websocket/overview)

## 3.2、@filter Jwt Auth
> 定义api的过滤器（路由中间件）
多个过滤器，使用空格分开。
过滤器的包是以应用的`根目录为起点`。假如您是将过滤器放在application包中，则应将包名带，如：`@filter application.Jwt`

## 3.3、@di transaction default
> 如果api的入参中，用interface类型，且对应的ioc名称是非空的（注册时指定了名称），则在这里显示指定ioc的名称
这里显示指定`transaction`接口的ioc名称为default

## 3.4、@message 下单成功
> 如果启用了ApiResponse中间件，需要显示定义接口返回的StatusMessage信息，则可以在这里指定，否则默认为：成功

## 4、生成路由
在项目的根目录执行：`fsctl -r`后，会自动在您的项目根目录中生成：`route.go`文件
```txt
Mac-mini shopping % fsctl -r
找到路由：area=/api/1.0/, [GET]/api/1.0/order/List ==> orderApp.List
找到路由：area=/api/1.0/, [GET]/api/1.0/order/Count ==> orderApp.Count
找到路由：area=/api/1.0/, [GET]/api/1.0/cate/List ==> procateApp.List
找到路由：area=/api/1.0/, [GET]/api/1.0/product/info ==> productApp.ToEntity
找到路由：area=/api/1.0/, [GET]/api/1.0/product/List ==> productApp.List
找到路由：area=/api/1.0/, [POST]/api/1.0/product/Buy ==> productApp.Buy
共生成路由：6条，代码审核：0条（0%）
成功...
```

## 5、引用route.go
```go
package main

import (
	"github.com/farseer-go/fs"
	"github.com/farseer-go/webapi"
)

func main() {
	fs.Initialize[StartupModule]("demo")
	webapi.RegisterRoutes(route...)

	// 让所有的返回值，包含在core.ApiResponse中
	webapi.UseApiResponse()
	// 使用静态文件 在根目录./wwwroot中的文件
	webapi.UseStaticFiles()
	// 运行web服务，端口配置在：farseer.yaml Webapi.Url 配置节点
	webapi.Run()
}
```

`webapi.RegisterRoutes(route...)`则是引用这个route.go文件。
后面，如果你有新的api或有调整，只需要重新运行`fsctl -r`，便可自动生成路由信息