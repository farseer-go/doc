# times时间处理
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/utils)

> 包：`"github.com/farseer-go/utils/times"`

## `GetDesc` 返回时间中文的描述
```go
// GetDesc 返回时间中文的描述
//	return "1 小时 32 分"
func GetDesc(ts time.Duration) string
```

## `GetSubDesc` 两个时间相减，返回时间中文的描述
```go
// GetSubDesc 两个时间相减，返回时间中文的描述
//	return "1 小时 32 分"
func GetSubDesc(ts1 time.Time, ts2 time.Time) string
```

## `GetTime` 根据time.Duration转换成天、小时、分钟、秒
```go
// GetTime 根据time.Duration转换成天、小时、分钟、秒
func GetTime(d time.Duration) (days int, hours int, minutes int, seconds int)
```