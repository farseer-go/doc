# snowflake雪花算法
> 包：`"github.com/farseer-go/fs/snowflake"`

```go
func GenerateId() int64 {}
```

## 生成随机数字
```go
return snowflake.GenerateId()
```
_运行结果：_

?> 193337022963818496

> 此函数将返回一个`18位长度`的随机`int64`

!> `fs.AppId` 用的就是`snowflake.GenerateId()`生成出来应用ID