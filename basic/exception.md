# exception异常处理
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/fs)
> 包：`"github.com/farseer-go/fs/exception"`

在go中`不建议使用panic来抛出异常`，建议使用`多参数返回值`来达到类似的效果。

!> 但有时候，使用类似.net core 、java这种异常机制来处理，反而会使得逻辑更加通顺

exception包，提供了3种异常类型：
- `exception.Exception`
- `exception.RefuseException`
- `exception.WebException`

提供了捕获异常函数：`exception.Try`

## 抛出Exception异常
```go
exception.ThrowException(err string)
exception.ThrowExceptionf(format string, a ...any)
```
这个异常只是对`panic(err)`的一个包装，一般用于不需要捕获时使用

## 抛出RefuseException异常
```go
exception.ThrowRefuseException(err string)
exception.ThrowRefuseExceptionf(format string, a ...any)
```
`RefuseException异常`，一般是用于`业务上作为拒绝而抛出`的。通常`需要上层捕获并处理自己的逻辑`。
