# flog日志
> 包：`"github.com/farseer-go/fs/flog"`

一共提供了`6个`日志级别，每个级别在打印日志后，会显示当前日志级别，如：
```go
flog.Infof("Web服务已启动：http://localhost%s/", addr)
```
_运行结果：_

?> 2022-12-01 17:07:24 [Info] Web服务已启动：http://localhost:8888/

> 格式为：`dateTime logLevel content`

!> 推荐大家使用flog日志实现应用的日志打印，我们将在`未来的版本`中会加入`分布式日志`能力，如果你使用了flog打印的日志，到时只需要修改配置就可实现自动采集日志
## Trace
```go
func Trace(contents ...any)
func Tracef(format string, a ...any)
```

## Debug
```go
func Debug(contents ...any)
func Debugf(format string, a ...any)
```

## Info
```go
func Info(contents ...any)
func Infof(format string, a ...any)
```

## Warning
```go
func Warning(content ...any)
func Warningf(format string, a ...any)
```

## Error
```go
func Error(contents ...any)
func Errorf(format string, a ...any)
```

## Critical
```go
func Critical(contents ...any)
func Criticalf(format string, a ...any)
```

## Log
```go
func Log(logLevel eumLogLevel.Enum, contents ...any)
```
