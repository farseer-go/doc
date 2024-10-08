# ListAny集合
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/collections)
> 包：`"github.com/farseer-go/collections"`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/collections)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/collections)
![](https://img.shields.io/github/v/release/farseer-go/collections)
[![codecov](https://img.shields.io/codecov/c/github/farseer-go/collections)](https://codecov.io/gh/farseer-go/collections)
![](https://img.shields.io/github/languages/code-size/farseer-go/collections)
[![Build](https://github.com/farseer-go/collections/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/collections/actions/workflows/test.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/collections)

`collections.ListAny`实际就是`collections.List[any]`，从它的定义就能看出来：
```go
type ListAny struct {
	List[any]
}
```

`collections.ListAny`在做一些底层数据存储时特别有用。比如在不用泛型又需要存储任意数据时，则可以使用该类型。