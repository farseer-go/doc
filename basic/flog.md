# flog日志
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/fs)
> 包：`"github.com/farseer-go/fs/flog"`

flog日志包一共提供了`6个`日志级别，每个级别在打印日志后，会显示当前日志级别。

同时支持多个记录器：`控制台打印`、`本地文件记录`、`数据库记录`、`Elasticsearch记录`、`MQ记录`等等。

flog设计为可任意扩展记录器，如以上无法满足你的需求时，你也可以`自定义记录器`，以此支持你的分布式日志。

另外，打印的格式支持：`text`、`json两个格式`（也可扩展更多的格式）

记录器的设置与打印格式，均通过./farseer.yaml来配置。

!> 推荐大家使用flog日志实现应用的日志打印，后续新增的能力只需要修改配置就可实现对应的功能。

## 6个日志级别
```go
flog.Trace("")
flog.Tracef("")
flog.Debug("")
flog.Debugf("")
flog.Info("")
flog.Infof("")
flog.Warning("")
flog.Warningf("")
flog.Error("")
flog.Errorf("")
flog.Critical("")
flog.Criticalf("")
flog.Print("")      // 不带日志级别
flog.Printf("")     // 不带日志级别
flog.Println("")    // 不带日志级别
flog.ErrorIfExists(fmt.Errorf("test error")) // 打印Error日志（假如存在）
```
如：
```go
// 打印结果： 2022-12-01 17:07:24 [Info] Web服务已启动：http://localhost:8888/
flog.Infof("Web服务已启动：http://localhost%s/", addr)
```

## 1、./farseer.yaml配置
```yaml
Log:
  Default:
    LogLevel: "info"          # 只记录级别>=info的日志内容
    Format: "json"            # 默认使用json格式输出
  Console:
    LogLevel: "info"          # 只记录级别>=info的日志内容
    Format: "text"            # 控制台打印，使用text格式输出
  File:
    LogLevel: "info"          # 只记录级别>=info的日志内容
    Format: "text"            # 使用text格式写入日志文件
    Path: "./log"             # 日志文件存储在应用程序的./log目录中
    RollingInterval: "Hour"   # 滚动间隔（Hour|Day|Week|Month|Year）
    FileSizeLimitMb: 1        # 文件大小限制
    FileCountLimit: 20        # 文件数量限制
    RefreshInterval: 1        # 写入到文件的时间间隔，秒单位，最少为1
  Database:
  ElasticSearch:
  Component:
    task: true                # 打印task组件的日志
    cacheManage: true         # 打印cacheManage组件的日志
    webapi: true              # 打印webapi组件的日志
    event: true               # 打印event组件的日志
    httpRequest: true         # 打印httpRequest组件的日志
    queue: true               # 打印queue组件的日志
    fSchedule: true           # 打印fSchedule组件的日志
```

## Log.Default配置
配置记录器的默认格式。即当对应的记录器没有配置时，则使用Log.Default的配置。

## Log.Console配置
配置日志打印到控制台的格式，如果没有配置，则使用Log.Default的配置。

## Log.File配置
配置日志写入到文件
* 可配置`RollingInterval`设置文件分类
* 可配置`FileSizeLimitMb`设置每个文件的大小
* 可配置`FileCountLimit` 限定日志文件的数量（超出时，自动删除最早的文件）
* 可配置`RefreshInterval` 控制日志写入到文件的频率，打印的日志会先写入缓冲区，然后再定期写入文件

## Log.Database配置
未实现

## Log.ElasticSearch配置
未实现