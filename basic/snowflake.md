# snowflake雪花算法
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/fs)
> 包：`"github.com/farseer-go/fs/snowflake"`

很多时候，我们需要生成一个唯一标识，但又不希望使用`Mysql的Id自增`或`Redis自增`时，可以采用本地生成的方式。

生成唯一标识就一个函数，它返回int64类型：
```go
func GenerateId() int64 {}
```

## 1、使用示例
```go
return snowflake.GenerateId()
```
_运行结果：_

?> 193337022963818496

> 此函数将返回一个`18位长度`的随机`int64`

!> `fs.AppId` 用的就是`snowflake.GenerateId()`生成出来应用ID