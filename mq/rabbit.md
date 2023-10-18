# rabbit client
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/rabbit)
> 包：`"github.com/farseer-go/rabbit"`
>
> 模块：`rabbit.Module`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/rabbit)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/rabbit)
![](https://img.shields.io/github/v/release/farseer-go/rabbit)
![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/rabbit)
![](https://img.shields.io/github/languages/code-size/farseer-go/rabbit)
[![Build](https://github.com/farseer-go/rabbit/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/rabbit/actions/workflows/build.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/rabbit)

## 1、概述
rabbit是比较流行的MQ中间件之一。

它有部署简单、使用方便、轻量等特性。

在系统资源占用上相对kafka（JAVA)、rockerMQ（JAVA)而言会更加少。

`farseer-go/rabbit`可以让应用系统更加优雅的使用rabbit：

- `配置自动化`：通过配置，不需要你手动创建交换器、队列和绑定他们的关系。
- `容器化操作`：使用IOC，我们可以很方便就能取到client。
- `支持连接池`：并允许设置Channel最低、最大值来控制连接池。

## 2、配置
_./farseer.yaml_
```yaml
Rabbit:
  Ex1: "Server=rabbit:5672,UserName=farseer,Password=farseer,Exchange=Ex1,RoutingKey=,Type=fanout,UseConfirm=false,AutoCreate=true"
  Ex2: "Server=rabbit:5672,UserName=farseer,Password=farseer,Exchange=Ex2,RoutingKey=,Type=fanout,UseConfirm=false,AutoCreate=true"
```
配置文件支持同时配置多个不同服务端、不同的交换器设置。

配置说明：
- Server（服务端设置）
  - `Server`：服务端地址
  - `UserName`：用户名
  - `Password`：密码
  - `MinChannelCount`：最多可以创建多少个频道
  - `MaxChannelCount`：最低保持多少个频道
- Exchange（交换器设置）
  - `ExchangeName`：交换器名称
  - `RoutingKey`：默认路由KEY
  - `ExchangeType`：交换器类型（fanout、direct、topic）
  - `IsDurable`：true表示 持久化，交换器、消息体、队列，均会设置为持久化
  - `AutoDelete`：不使用时，是否自动删除
  - `UseConfirmModel`：发送消息时，是否需要确保消息发送到服务端
  - `AutoCreateExchange`：交换器不存在，是否自动创建

## 3、交换器类型
### 3.1、`fanout`
所有对该交换器类型的队列，均可消费到全量的消息
### 3.2、`direct`
绑定到该交换器的队列需要设定好RoutingKey。 生产者对交换器发消息的同时携带RoutingKey

交换器根据RoutingKey，自动将消息发送到对应RoutingKey的队列中。

该RoutingKey必须完全匹配。

比如在多租户下，我们可以将公司ID作为RoutingKey。这样每个租户的队列可以独立消费，彼此不受影响。
### 3.3、`topic`
与`direct`的完全匹配机制不同的是，`topic`支持通配符匹配机制

支持的符号有：`*`、`#`

符号`#`匹配一个或多个词，符号`*`匹配不多不少一个词。因此“audit.`#`”能够匹配到“audit.irs.corporate”，但是“audit.`*`” 只会匹配到“audit.irs”。
## 4、发消息
```go
product := container.Resolve[rabbit.IProduct]("Ex1")
product.SendString("aaaa")
```
通过`container.Resolve`容器取出`rabbit.IProduct`接口的实现。

参数值`Ex1`，是在`./farseer.yaml`中设置的`ExchangeName`，意味着使用这个配置来发消息。

如果配置了`AutoCreateExchange=true`，则如果交换器不存在时，会自动创建。

`SendString`函数，代表要发送`string类型`的消息。

接下来，看下`rabbit.IProduct`接口的其它函数
```go
type IProduct interface {
	// SendString 发送消息（使用配置设置）
	SendString(message string) error
	// SendJson 发送消息，将data序列化成json（使用配置设置）
	SendJson(data any) error
	// SendStringKey 发送消息（使用配置设置）
	SendStringKey(message, routingKey string) error
	// SendJsonKey 发送消息（使用配置设置）
	SendJsonKey(data any, routingKey string) error
	// SendMessage 发送消息
	SendMessage(message []byte, routingKey, messageId string, priority uint8) error
}
```
其中`data any`，意味着可以传入任意类型，这时函数会对data做json序列化处理。

?> 事实上，以上的函数，最终会调用：`SendMessage`函数

## 5、消费
```go
// 注册消费者
consumer := container.Resolve[rabbit.IConsumer]("Ex1")
// 手动ACK
consumer.SubscribeAck("Q1", "", func(message string, ea rabbit.EventArgs) bool {
    return true
})

// 自动ACK
consumer.Subscribe("Q2", "", func(message string, ea rabbit.EventArgs) {
	// doing...
})
```
通过`container.Resolve`容器取出`rabbit.IConsumer`接口的实现。

参数值`Ex1`，是在`./farseer.yaml`中设置的`ExchangeName`，意味着使用这个配置来消费消息。

`consumer.Subscribe`函数将订阅rabbit的queue。

- 第一个参数：队列名称，如果不存在，则自动创建（示例中的为Q1）
- 第二个参数：routingKey，路由键
- 第三个参数：应用系统的逻辑处理实现。
- message：在队列中推送过来的消息内容。
- ea：本条消息的属性。

`SubscribeAck函数`：
只有当return true时，本条消息才确认为成功消费，否则还会重新排入到队列中。

`Subscribe函数`：
只要收到消息，即从队列中移除该条消息