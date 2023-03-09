# flog日志
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/fs)
> 包：`"github.com/farseer-go/fs/flog"`

一共提供了`6个`日志级别，每个级别在打印日志后，会显示当前日志级别，如：
```go
flog.Infof("Web服务已启动：http://localhost%s/", addr)
```
_运行结果：_

?> 2022-12-01 17:07:24 [Info] Web服务已启动：http://localhost:8888/

> 格式为：`dateTime logLevel content`

!> 推荐大家使用flog日志实现应用的日志打印，我们将在`未来的版本`中会加入`分布式日志`能力，如果你使用了flog打印的日志，到时只需要修改配置就可实现自动采集日志

## 1、./farseer.yaml配置
```yaml
Log:
  LogLevel: "Information" // 低于info级别不打印
  Component:
    task: true            // 打印task组件的日志
    cacheManage: true     // 打印cacheManage组件的日志
    webapi: true          // 打印webapi组件的日志
    event: true           // 打印event组件的日志
    httpRequest: true     // 打印httpRequest组件的日志
    queue: true           // 打印queue组件的日志
    fSchedule: true             // 打印fSchedule组件的日志
```
?> 通过配置文件`./farseer.yaml`，可以用来控制在生产环境、开发环境、测试环境间的日志打印频率

Log.LogLevel控制日志打印的最低级别，级别从低到高：
- Trace 
- Debug 
- Information 
- Warning 
- Error 
- Critical

!> 如果`Log.LogLevel`设为"`Information`"，则`Trace`、`Debug`都不会打印出来
## 2、Trace
```go
func Trace(contents ...any)
func Tracef(format string, a ...any)
```

## 3、Debug
```go
func Debug(contents ...any)
func Debugf(format string, a ...any)
```

## 4、Info
```go
func Info(contents ...any)
func Infof(format string, a ...any)
```

## 5、Warning
```go
func Warning(content ...any)
func Warningf(format string, a ...any)
```

## 6、Error
```go
func Error(contents ...any)
func Errorf(format string, a ...any)
```

## 7、Critical
```go
func Critical(contents ...any)
func Criticalf(format string, a ...any)
```

## 8、Log
```go
func Log(logLevel eumLogLevel.Enum, contents ...any)
```
