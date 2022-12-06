# List集合
> 包：`"github.com/farseer-go/collections"`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/collections)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/collections)
![](https://img.shields.io/github/v/release/farseer-go/collections)
![](https://img.shields.io/github/languages/code-size/farseer-go/collections)
![](https://img.shields.io/github/directory-file-count/farseer-go/collections)
![](https://img.shields.io/github/last-commit/farseer-go/collections)

go内置的集合只有数组和切片，有时候我们需要更多高级的数据处理功能，比如：`排序、去重、交集、并集、聚合`操作时，需要编写非常多的代码才能实现。

List集合提供了`50多个数据操作`功能，可以完全代替go原生的数组类型。

## 数据结构定义
```go
type List[T any] struct 
```

## 1、初始化集合
```go
// 第一种
var lst collections.List[int]
lst = collections.NewList[int](1, 2, 3, 4, 5, 6)

// 第二种
lst2 := collections.NewList[string]("a", "b", "c", "d")
```

## 2、添加元素
```go
lst := collections.NewList[int]()
lst.Add(1, 2, 3) // lst = 1, 2, 3
lst.Insert(1, 8) // lst = 1, 8, 2, 3
lst.Count()      // return 4
```

## 3、删除元素
```go
lst := collections.NewList[int](1, 2, 3, 4, 5, 6)
lst.RemoveAt(3)  // lst = 1, 2, 3, 5, 6
lst.Remove(5)  // lst = 1, 2, 3, 6
lst.RemoveAll(func(item int) bool {  // count = 2
    return item >= 3
})
list.Clear() // count = 0
```

## 4、获取元素
```go
lst := collections.NewList[int](1, 2, 3, 4, 5, 6)
lst.First()     // return 1
lst.Last()      // return 6
lst.Index(4)    // return item 5
lst.IndexOf(4)  // return index 3
```

## 5、条件筛选
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

## 6、Take、Skip
```go
lst := collections.NewList[int](1, 2, 3, 4, 5)
lst1 = lst.Take(3).ToList() // lst1 = 1, 2, 3
lst2 = lst.Skip(2).ToList() // lst2 = 3, 4, 5
```

## 7、聚合操作
```go
lst := collections.NewList[int](1, 2, 3, 4, 5)
// 求总和----------------------------------------------------->
lst.SumItem()                       // return 15
lst.Sum(func(item int) any {        // return 10
    return item - 1
})

// 求平均----------------------------------------------------->
lst.AverageItem()                   // return 3
lst.Average(func(item int) any {    // return 2
    return item - 1
})

// 最小值----------------------------------------------------->
lst.Min(func(item int) any {        // return 0
    return item - 1
})
lst.MinItem()                       // return 1

// 最大值----------------------------------------------------->
st.Max(func(item int) any {         // return 4
    return item - 1
})
lst.MaxItem()                       // return 5
```

## 8、GroupBy分组
```go
type testItem struct {
    name string
    age  int
}
lst := collections.NewList[testItem](testItem{name: "steden", age: 36}, testItem{name: "steden", age: 18}, testItem{name: "steden2", age: 40})
var lstMap map[string][]testItem
lst.GroupBy(&lstMap, func(item testItem) any {
    return item.name
})

len(lstMap)                         // return 2
len(lstMap["steden"]) != 2          // return 2
len(lstMap["steden2"])              // return 1
lstMap["steden"][0].age             // return 36
lstMap["steden"][1].age             // return 18
lstMap["steden2"][0].age            // return 40
```

## 9、Order排序
```go
lst := collections.NewList(3, 5, 6, 2, 1, 8, 7, 4)
// 正序----------------------------------------------------->
lst.OrderByItem().ToArray()         // return 1, 2, 3, 4, 5, 6, 7, 8
lst.OrderBy(func(item int) any {    // return 1, 2, 3, 4, 5, 6, 7, 8
    return item
}).ToArray()

// 倒序----------------------------------------------------->
lst.OrderByDescendingItem().ToArray()       // return 8, 7, 6, 5, 4, 3, 2, 1
lst.OrderByDescending(func(item int) any {  // return 8, 7, 6, 5, 4, 3, 2, 1
    return item
}).ToArray()
```

## 10、并集、并集、差集
```go
lst1 := collections.NewList(1, 2, 3)
lst2 := collections.NewList(3, 4, 5)
lst1.Intersect(lst2)                    // return NewList(3)
lst1.Concat(lst2)                       // return NewList(1, 2, 3, 3, 4, 5)
lst1.Union(lst2)                        // return NewList(1, 2, 3, 4, 5)
NewList(1, 2, 3, 3, 4, 5).Distinct()    // return NewList(1, 2, 3, 4, 5)
lst1.Except(lst2)                       // return NewList(1, 2)
```

## 11、Select投影
```go
// []string----------------------------------------------------->
lst := collections.NewList("1", "", "2")
var arr []string
lst.Select(&arr, func(item string) any {    // arr = {"go:1", "go:", "go:2"}
    return "go:" + item
})
// List[string]----------------------------------------------------->
var lstSelect List[string]
lst.Select(&lstSelect, func(item string) any {
    return "go:" + item
})
```
## 12、SelectMany投影
```go
lst := collections.NewList([]string{"1", "2"}, []string{"3", "4"})
var arr []string
lst.SelectMany(&arr, func(item []string) any {  // arr = "1", "2", "3", "4"
    return item
})

var lst2 List[string]
lst.SelectMany(&lst2, func(item []string) any {
    return item
})
```

## 13、ToMap转成字典
```go
type testItem struct {
    name string
    age  int
}
lst := collections.NewList[testItem](testItem{name: "steden", age: 36}, testItem{name: "steden", age: 18}, testItem{name: "steden2", age: 40})
var lstMap map[string][]int

lst.ToMap(&lstMap,    // ["steden"][0] = 36, ["steden"][1] = 18, ["steden2"][0] = 40
    func(key testItem) any {
        return key.name
    }, func(value testItem) any {
        return value.age
    })
```

## 14、ToPageList转成collections.PageList类型
```go
lst := collections.NewList(1, 2, 3, 4, 5, 6, 7)
item := lst.ToPageList(3, 2)     // item.RecordCount = 7    item.List = NewList(4, 5, 6)
```

## 15、MapToList 两个List集合转换
```go
type po struct {
    Name string
    Age  int
}
type do struct {
    Name string
    Age  int
}

lst := collections.NewList(po{Name: "steden", Age: 37})
var lstDO List[do]
lst.MapToList(&lstDO) // lstDO.First().Name = "steden"   lstDO.First().Age = 37
```

## 16、MapToArray 转换到数组
```go
type po struct {
Name string
Age  int
}
type do struct {
Name string
Age  int
}

lst := collections.NewList(po{Name: "steden", Age: 37})
var lstDO []do
lst.MapToArray(&lstDO) // lstDO[0].Name = "steden"   lstDO[0].Age = 37
```

## 17、ToListAny 转换成collections.ListAny类型
```go
type po struct {
    Name string
    Age  int
}
lst := collections.NewList(po{Name: "steden", Age: 36}, po{Name: "steden", Age: 18}, po{Name: "steden2", Age: 40})
lst.ToListAny()  // return collections.ListAny
```

## 18、Range 获取范围内的集合
```go
lst1 := collections.NewList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
lst1.Range(3, 2)    // return lstCut[0] = 4, lstCut[1] = 5
lst1.RangeStart(7)  // return lstCut[0] = 8, lstCut[1] = 9, lstCut[2] = 10
```

## 19、Rand 集合中获取随机的元素
```go
NewList(1, 2, 3).Rand() // return 1 or 2 or 3
```

## 20、ToString 转成字符串
```go
NewList(1, 2, 3).ToString(",") // return "1,2,3"
```