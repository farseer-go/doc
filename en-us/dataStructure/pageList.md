# pageList分页
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

PageList实质就是在List的基础上，增加了一个`RecordCount`字段，在`分页数据`的场景下一般都会使用`PageList类型`来返回

## 数据结构定义
```go
type PageList[TData any] struct {
    // 总记录数
    RecordCount int64
    // 数据列表
    List List[TData]
}
```

## 初始化
```go
lst := collections.NewList[string]("a", "b", "c", "d")
collections.NewPageList(lst, 100)
```

?> 100：表示这批数据，一共有100条记录（RecordCount=100）。

## MapToPageList 转换到另一个PageList类型
_fops中用到的场景_
```go
func (repository buildRepository) ToBuildingList(pageSize int, pageIndex int) collections.PageList[build.DomainObject] {
	pageList := repository.Select("Id", "Status", "build_number", "IsSuccess", "project_id", "ProjectName", "CreateAt", "FinishAt", "ClusterId").Order("Id desc").ToPageList(pageSize, pageIndex)
	
	var lst collections.PageList[build.DomainObject]
	pageList.MapToPageList(&lst)
	return lst
}
```

?> 比如在上面这个场景，通常不会把PO直接对外提供，而是会转换成DO或DTO来提供对外使用。