# Dictionary集合
> 包：`"github.com/farseer-go/collections"`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/collections)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/collections)
![](https://img.shields.io/github/v/release/farseer-go/collections)
![](https://img.shields.io/github/languages/code-size/farseer-go/collections)
[![Build](https://github.com/farseer-go/collections/actions/workflows/go.yml/badge.svg)](https://github.com/farseer-go/collections/actions/workflows/go.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/collections)

Dictionary是字典的意思，是go内置类型map的代替者，是一种更加高级的数据操作方案

## 数据结构定义
```go
type Dictionary[TKey comparable, TValue any] struct
```

## 初始化字典
```go
// 第一种
var dic collections.Dictionary[int,string]
dic = collections.NewDictionary[int,string]()

// 第二种
maps := make(map[string]string)
maps["name"] = "steden"
maps["age"] = "18"
dic2 := collections.NewDictionaryFromMap(maps)
```

## 1、Add 添加kv
```go
dic = collections.NewDictionary[string,string]()
dic.Add("name", "steden") // add key=name,value=steden
```

## 2、AddMap 添加来自map的数据
```go
dic := NewDictionary[string, int]()
maps := map[string]int{"age": 18}
dic.AddMap(maps)
```

## 3、GetValue 获取指定Key的数据
```go
maps := make(map[string]string)
maps["name"] = "steden"
maps["age"] = "18"
dic := collections.NewDictionaryFromMap(maps)

dic.GetValue("name") // return "steden"
```

## 4、ContainsKey 是否包含Key
```go
maps := make(map[string]string)
maps["name"] = "steden"
maps["age"] = "18"
dic := collections.NewDictionaryFromMap(maps)

dic.ContainsKey("name") // return true
```

## 5、ContainsValue 是否包含Value
```go
maps := make(map[string]string)
maps["name"] = "steden"
maps["age"] = "18"
dic := collections.NewDictionaryFromMap(maps)

dic.ContainsValue("steden") // return true
```

## 6、Keys 获取所有Key
```go
maps := make(map[string]string)
maps["name"] = "steden"
maps["age"] = "18"
dic := collections.NewDictionaryFromMap(maps)

dic.Keys() // collections.List[string]: "name"、"age"
```

## 7、Values 获取所有Values
```go
maps := make(map[string]string)
maps["name"] = "steden"
maps["age"] = "18"
dic := collections.NewDictionaryFromMap(maps)

dic.Values() // collections.List[string]: "steden"、"18"
```

## 8、Count 获取数量
```go
maps := make(map[string]string)
maps["name"] = "steden"
maps["age"] = "18"
dic := collections.NewDictionaryFromMap(maps)

dic.Count() // return 2
```

## 9、Remove 移除Key
```go
maps := make(map[string]string)
maps["name"] = "steden"
maps["age"] = "18"
dic := collections.NewDictionaryFromMap(maps)

dic.Remove("name") // remove key name
```

## 10、Clear 清空所有数据
```go
maps := make(map[string]string)
maps["name"] = "steden"
maps["age"] = "18"
dic := collections.NewDictionaryFromMap(maps)

dic.Clear() // clear all item
```

## 11、ToMap 转换成map
```go
maps := make(map[string]string)
maps["name"] = "steden"
maps["age"] = "18"
dic := collections.NewDictionaryFromMap(maps)

dic.ToMap() // 转换成map[string]string
```