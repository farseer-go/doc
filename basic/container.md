# container容器
> 包：`"github.com/farseer-go/fs/container"`

在使用前，我们先定义一个接口、以及实现该接口的结构体

_ITest.go 接口_
```go
// 接口
type ITest interface {
    TestCall() string
}
```
_testStruct.go 实现ITest接口_
```go
type testStruct struct { }

// 实现ITest接口
func (r testStruct) TestCall() string {
    return "hello world"
}
```

## 注册单例
```go
container.Register(func() ITest { 
	return testStruct{}
})
```
`container.Register`函数 要求传入一个`func函数`，该函数要求`返回接口类型`（这里是ITest）。函数体就是你如何实例化这个对象

这样我们就注册了一个`单例`ITest接口的容器


## 注册临时对象
要注册临时对象，需要利用`container.Use`函数来设置：
```go
container.Use[ITest](func() ITest { 
	return testStruct{} 
}).Transient().Register()
```
当调用了`Transient()方法`时，意味着当前注册的对象为`临时对象`，每次获取注册的容器时，都会执行一遍func()函数

## 注册多实例

对于应用来说，大部份的使用场景都是一对一的。但有时候我们还是需要`一个接口对应多个不同的实现`，比如`工厂模式`。

这时我们就要用到`container.Use`函数了：
```go
container.Use[ITest](func() ITest { 
	return testStruct{} 
}).Name("test1").Register()

container.Use[ITest](func() ITest {
    return testStruct{} 
}).Name("test2").Register()
```

通过`Name方法`，可以为注册的实例增加`别名属性`。如上面例子，我们为ITest接口`注册了两个实例`。

这时我们可以通过`container.ResolveName`函数，获取`不同别名的实现`：
```go
instance := container.ResolveName[ITest]("test")
```

## 获取实例
```go
instance:= container.Resolve[ITest]()
```
`container.Resolve`接受一个泛型，这个泛型传入我们需要获取实例对象的`继承接口`。

这样我们就可以拿到ITest的实例了。

!> 使用容器，可以使得我们的接口与实现进行解耦，让我们的代码更清晰。很好的实现了`依赖倒置`的设计原则

知识扩充：

?> 比如我们可以将ITest`接口放在domain领域层`，而`实现放在infrastructure基础设施层`，做到完全的分离。

如果你在注册时，调用了`Name()方法`，则在获取时，应使用`container.ResolveName`:
```go
instance := container.ResolveName[ITest]("test")
```