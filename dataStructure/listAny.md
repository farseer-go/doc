# ListAny集合
> 包：`"github.com/farseer-go/collections"`

`collections.ListAny`实际就是`collections.List[any]`，从它的定义就能看出来：
```go
type ListAny struct {
	List[any]
}
```

`collections.ListAny`在做一些底层数据存储时特别有用。比如在不用泛型又需要存储任意数据时，则可以使用该类型。