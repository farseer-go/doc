# stopwatch时间统计
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/fs)
> 包：`"github.com/farseer-go/fs/stopwatch"`

很多时候，我们需要知道一个函数`执行了多长时间`，那么就可以使用stopwatch来`统计时间消耗`。

## 1、StartNew 创建实例
```go
// new
sw := stopwatch.StartNew()

// sleep 1 second
time.Sleep(time.Second)

flog.Println("finish，use：" + strconv.FormatInt(sw.ElapsedMilliseconds(), 10) + " ms")
```
_运行结果：_

?> "finish，use：1000 ms"

> `StartNew()` 函数表示：创建新实例并开始统计。

?> 通过`sw.ElapsedMilliseconds()`方法，可以拿到`时间消耗统计`了。`单位：ms`。

除此之外，还可以对统计进行更加细腻度控制：
```go
sw := stopwatch.New()
// Start or Continue timing
sw.Start()
// Reset Timer
sw.Restart()
// Timer pause
sw.Stop()
// Return to timed time
sw.ElapsedMilliseconds()
```

## 2、New()
只实例化对象，但不统计时间消耗（状态：未开始）。
## 3、Start()
开始统计时间消耗。
## 4、Restart()
重置之前的统计，并重新开始统计。
## 5、Stop()
暂停统计。

?> 暂停后，可以再次调用`Start()`来继续统计，时间将会把暂停前的时间统计在内。