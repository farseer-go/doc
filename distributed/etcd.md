# etcd client
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/etcd)
> 包：`"github.com/farseer-go/etcd"`
>
> 模块：`etcd.Module`
  
![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/etcd)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/etcd)
![](https://img.shields.io/github/v/release/farseer-go/etcd)
![go-version](https://img.shields.io/github/go-mod/go-version/farseer-go/etcd)
![](https://img.shields.io/github/languages/code-size/farseer-go/etcd)
[![Build](https://github.com/farseer-go/etcd/actions/workflows/build.yml/badge.svg)](https://github.com/farseer-go/etcd/actions/workflows/build.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/etcd)

## 概述
etcd是比较流行的分布式组件之一，另外还有Zookeeper、Eureka、Nacos、Consul

常用于在分布式平台中的服务注册与发现的场景。

它有部署简单、使用方便、轻量等特性

在GO阵营里面，一般会优先选择etcd，因为它是采用GO语言编写的。

`farseer-go/etcd`可以让应用系统更加优雅的使用etcd：

- `配置自动化`：通过配置，可以很方便的快速接入ETCD服务
- `容器化操作`：使用IOC，我们可以很方便就能取到client。
- `优雅的使用`：将常用的操作，整合到几个方法中，更加简单使用。

## 配置
_./farseer.yaml_
```yaml
Etcd:
  default1: "Server=127.0.0.1:2379|127.0.0.1:2379,DialTimeout=5000,Username=test,Password=test"
  default2: "Server=127.0.0.1:2379|127.0.0.1:2379,DialTimeout=5000,Username=test,Password=test"
```
配置文件支持同时配置多个不同服务端。default1、default2是自定义的名称，**同时也是IOC的别名**

配置说明：
```go
type etcdConfig struct {
	Server               string // 服务端地址
	DialTimeout          int    // 连接超时时间（ms)
	DialKeepAliveTime    int    // 对服务器进行ping的时间（ms)
	DialKeepAliveTimeout int    // 客户端等待响应的超时时间（ms)
	MaxCallSendMsgSize   int    // 客户端的请求发送限制，单位是字节。0，则默认为2.0 MiB（2 * 1024 * 1024）。
	MaxCallRecvMsgSize   int    // 客户端的响应接收限制，单位是字节。0，则默认不限制
	Username             string // 用户名
	Password             string // 密码
	RejectOldCluster     bool   // 拒绝过时的集群创建客户端。
	PermitWithoutStream  bool   // 允许客户端在没有任何活动流（RPC）的情况下向服务器发送keepalive pings。
}
```
配置的属性之间用`,`隔开组合成一个字符串，将被解析成etcdConfig对象。

## Put
保存KV
```go
client := container.Resolve[etcd.IClient]("default1") // 取出default1的配置服务端
putRsp, err := client.Put("/test/a1", "1")
```
通过`container.Resolve`容器取出`etcd.IClient`接口的实现。

参数值`default1`，是在`./farseer.yaml`中配置节点，意味着使用default1的配置服务端

`KEY`："/test/a1"，`Value`："1"

## Get
可以支持按KEY完整匹配，或者按KEY的前缀匹配。
```go
// 根据KEY完整切尔西
client := container.Resolve[etcd.IClient]("default1")
result, err := client.Get("/test/a1")
flog.Info(result.Value) // print:1

// 根据KEY前缀匹配
results, err := client.GetPrefixKey("/test")
flog.Info(results["/test/a1"].Value)    // print:1
```
results是map对象，map的key是string类型，从etcd中获取到的KEY
## Exists
判断KEY是否存在
```go
client := container.Resolve[etcd.IClient]("default1")
client.Exists("/test/a1")
```

## Delete
删除KEY
```go
client := container.Resolve[etcd.IClient]("default1")
_, _ = client.Delete("/test/a1")
```
删除后"/test/a1"将从etcd服务端移除

## Watch
监控指定的KEY（即使KEY还没有创建也可以先监控起来）

当这个KEY有任何的变化时，我们都可以拿到最新的数据状态
```go
client := container.Resolve[etcd.IClient]("default1")
ctx, cancelFunc := context.WithCancel(context.Background())
// 指定KEY
client.Watch(ctx, "/test/a1", func(event etcd.WatchEvent) {
    flog.Info(event.Kv.Value)   // value
    flog.Info(event.IsModify())
    flog.Info(event.IsCreate())
})

// 指定KEY前缀
client.WatchPrefixKey(ctx, "/test/", func(event etcd.WatchEvent) {
    flog.Info(event.Kv.Value)   // value
    flog.Info(event.IsModify())
    flog.Info(event.IsCreate())
})
```

## Lock
分布式锁
```go
client := container.Resolve[etcd.IClient]("default1")

unLock1, _ := client.Lock("/lock/1", 1)
flog.Info("上锁：unLock1")

go func() {
    time.Sleep(3000 * time.Millisecond)
    flog.Info("解锁：unLock1")
    unLock1()
}()

unLock2, _ := client.Lock("/lock/1", 3)
flog.Info("上锁：unLock2")

unLock2()
flog.Info("解锁：unLock2")
```
打印结果：
```
2023-01-22 15:10:00 [Info] 上锁：unLock1
2023-01-22 15:10:03 [Info] 解锁：unLock1
2023-01-22 15:10:03 [Info] 上锁：unLock2
2023-01-22 15:10:03 [Info] 解锁：unLock2
```
?> Lock方法第1个参数：KEY，第二个参数：TTL，如果应用程序退出进程，会在TTL + 1秒后自动解锁。如果的应用程序处理时间超过TTL，是会自动续约的，这点不用担心未处理完就解锁了。

!> 同一时刻同一个KEY，只能有一个客户端能上锁成功。使用完后，需手动调用unLock()解锁

## 租约
```go
client := container.Resolve[etcd.IClient]("default1")

// 先创建一个保持10秒的租约，拿到租约ID
leaseID, _ := client.LeaseGrant(10)

// 在给KV保存的时候，带上这个leaseID
_, _ = client.PutLease("/test/lease1", "1", leaseID)
```
!> 此后这个租约将在11秒后过期。（TTL + 1 秒）

`自动持续续约：`
```go
ctx, cancelFunc := context.WithCancel(context.Background())
_ = client.LeaseKeepAlive(ctx, leaseID)
```
此时，KEY:`/test/lease1`，将会自动续约，不再需要我们手动去执行续约操作。

`只续约一次：`
```go
_ = client.LeaseKeepAliveOnce(leaseID)
```
此时，只会续约一次，每次续约为10秒（创建租约时传了10）

## 使用原生客户端
有时候我们需要原生的client执行更多操作时，可以使用`Original`方法
```go
client := container.Resolve[etcd.IClient]("default")
client.Original()
```
将返回：`etcdV3.Client`对象

## 最后
以上是常用的方法。完整的方法列表，可以查看：`etcd.IClient`接口

## 运行etcd环境
```shell
docker network create net
docker run -d --name etcd \
  --network net \
  -p 2379:2379 \
  -p 2380:2380 \
  -e ETCD_NAME=etcd_master \
  -e ALLOW_NONE_AUTHENTICATION=yes \
  -e ETCD_ADVERTISE_CLIENT_URLS=http://127.0.0.1:2379 \
  bitnami/etcd:latest
```