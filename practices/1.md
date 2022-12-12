# linq在go中的使用
## 前言
在刚接触go时，被他的极简风格所震撼，与此同时对隐式接口的设计思想也是极度喜欢。

对于集合，go只提供了数组、切片、map类型，不像其它语言类型那样，会提供非常丰富的内置类型。

比方说，我们需要对切片实现：`排序、去重、聚合、条件筛选、GroupBy`这些基本操作时，就没有提供内置的方法。

看下面的需求：
```go
var arr:=[]int{4, 1, 5, 3, 4, 1}
```

需要将切片做去重操作时，就显得比较麻烦，需要我们自己写一个去重函数，然后调用后返回，可能是：
```go
var arr:=[]int{4, 1, 5, 3, 4, 1}
utils.Distinct(arr)  // 对切片的值做去重操作。
```

虽然也实现了这个需求，但需要我们实现utils.Distinct函数，并且这种方式显的很不优雅。

## linq
借助linq语法，可以让我们省去很多需要使用for循环对数组遍历的操作。

在go中也有类似的linq语法，它就是：`farseer-go/collections` [Github开源](https://github.com/farseer-go/collections) [官方文档](https://farseer-go.gitee.io/#/dataStructure/list)

接下来我们演示下操作：

### 1、初始化集合
```go
// 第一种
var lst collections.List[int]
lst = collections.NewList[int](1, 2, 3, 4, 5, 6)

// 第二种
lst2 := collections.NewList[string]("a", "b", "c", "d")

// 第三种
var arr:=[]int{4, 1, 5, 3, 4, 1}
lst3 := collections.NewList(arr...)
```

初始化后，会得到一个：`collections.List`这个类型，这个类型，提供了`50多个方法`供我们使用。

### 2、去重

```go
lst := collections.NewList(4, 1, 5, 3, 4, 1)
lst = lst.Distinct()
```
这时lst的值 = `4, 1, 5, 3`

### 3、排序
```go
lst := collections.NewList(4, 1, 5, 3, 4, 1)
lst = lst.Distinct()
lst = lst.OrderByItem()             // 正序
lst = lst.OrderByDescendingItem()   // 倒序

// 我们也可以合并到一起写：
lst2 := collections.NewList(4, 1, 5, 3, 4, 1)
lst2 = lst2.Distinct().OrderByItem()            // 正序
lst2 = lst2.Distinct().OrderByDescendingItem()  // 倒序
```

执行`OrderByItem()`后，拿到的是：`1, 3, 4, 5（正序）`，执行`OrderByDescendingItem()`后是：`5, 4, 3, 1（倒序）`

### 4、添加元素
这个是基本操作，使用也是很方便的
```go
lst := collections.NewList[int]()
lst.Add(1, 2, 3) // lst = 1, 2, 3
lst.Insert(1, 8) // lst = 1, 8, 2, 3
lst.Count()      // return 4
```
`Add`可以让我们在`尾部追加元素`，而`Insert`可以`指定在Index位置后面追加`。

### 5、删除元素
```go
lst := collections.NewList[int](1, 2, 3, 4, 5, 6)
lst.RemoveAt(3)  // lst = 1, 2, 3, 5, 6
lst.Remove(5)  // lst = 1, 2, 3, 6
lst.RemoveAll(func(item int) bool {  // count = 2
    return item >= 3
})
list.Clear() // count = 0
```

### 6、获取元素
```go
lst := collections.NewList[int](1, 2, 3, 4, 5, 6)
lst.First()     // return 1
lst.Last()      // return 6
lst.Index(4)    // return item 5
lst.IndexOf(4)  // return index 3
```

### 7、条件筛选
```go
lst := collections.NewList[int]()

// 是否有值----------------------------------------------------->
lst.Any()       // return false

// 是否为空----------------------------------------------------->
lst.IsEmpty()   // return true
lst.Add(1)
lst.Any()       // return true
lst.IsEmpty()   // return false

// 是否包含元素----------------------------------------------------->
lst.Contains(1) // return true
lst.Add(2, 3, 4, 5, 6)

// 条件筛选----------------------------------------------------->
lst = lst.Where(func(item int) bool {   // lst = 5, 6
    return item >= 3
}).Where(func(item int) bool {
    return item >= 5
}).ToList()

// 是否都满足----------------------------------------------------->
lst.All(func(item int) bool { // return true
    return item == 5 || item == 6
})
```

前面说到，这个List集合，是有`50多个方法`的。这里就不一一展示了，感兴趣的小伙伴可以到[官方文档](https://farseer-go.gitee.io/#/dataStructure/list)上学习。