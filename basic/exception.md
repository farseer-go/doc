# exception异常处理
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/fs)
> 包：`"github.com/farseer-go/fs/exception"`

在go中`不建议使用panic来抛出异常`，建议使用`error返回值`来达到类似的效果。

!> 但有时候，使用类似.net core 、java这种异常机制来处理，反而会使得逻辑更加通顺

exception包，提供了3种异常类型：
- `exception.Exception`
- `exception.RefuseException`
- `exception.WebException`

捕获异常：`exception.Try`

## 1、Exception异常
```go
exception.ThrowException(err string)
exception.ThrowExceptionf(format string, a ...any)
```
这个异常是对`panic(err)`的一个包装，一般用于非业务逻辑的错误、未知的异常

## 2、RefuseException异常
```go
exception.ThrowRefuseException(err string)
exception.ThrowRefuseExceptionf(format string, a ...any)
```
`RefuseException异常`，一般是用于`业务上作为拒绝`而使用的。通常`需要上层捕获并处理自己的逻辑`。

## 3、WebException异常
```go
exception.ThrowWebException(statusCode int, err string)
exception.ThrowWebExceptionf(statusCode int, format string, a ...any)
```

在`webapi`组件中，使用`minimalApi`时，由于我们的函数不会依赖任何的web组件，而又希望对接口返回错误，并`标记statusCode`时，则可以使用`WebException`异常，`webapi会对此异常捕获并设置http statusCode`

## 4、捕获异常
```go
func Run() {
    try := exception.Try(func() {
		test()
    }).CatchStringException(func(exp string) {
		// 这里不会运行，因为test使用的是RefuseException类型的异常
        flog.Info(exp)
    }).CatchRefuseException(func(exp exception.RefuseException) {
		// 这里会运行，因为捕获到了RefuseException类型的异常
        flog.Warning(exp.Message)
    }).CatchException(func(exp any) {
		// 这里不会运行，因为CatchRefuseException已经捕获到了
        flog.Error(exp)
    })
}

func test()  {
    // 这里我们使用一个异常
    exception.ThrowRefuseException("test is throw")
}
// print: [Warn] test is throw
```

异常的捕获提供了`4`个方法：
- CatchStringException：可以捕获`panic(string)`、`Exception` 这两种类型的异常
- CatchRefuseException：可以捕获`RefuseException` 类型的异常
- CatchWebException：可以捕获`WebException` 类型的异常
- CatchException：可以捕获`所有类型`的异常

!> 如果上层先匹配捕获到了，则后面不会被再次捕获到，因为前面已经处理了这个异常。

?> 如果在捕获的过程中，又调用了`exception.ThrowXXX`，则后面的`CatchXXX`会继续匹配。

## 5、向上暴露异常
未能匹配到异常类型时，如果希望此异常继续向上暴露时，可以调用`ThrowUnCatch()`方法：
```go
func Run() {
    try := exception.Try(func() {
		test()
    }).CatchStringException(func(exp string) {
		// 这里不会运行，因为test暴露的是RefuseException类型的异常
        flog.Info(exp)
    }).ThrowUnCatch()
}

func test()  {
    exception.ThrowRefuseException("test is throw")
}
```

## 6、什么时候会用到异常机制
一般来说，如果接口的定义是固定且无法变更的（如第三方框架），并且这种方法的返回值又不支持error的时候。

而你的业务需要去识别不同的结果时，则使用异常机制是很有必要的。