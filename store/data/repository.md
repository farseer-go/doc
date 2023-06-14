# 通用仓储

在应用系统中，我们经常会对不同的表执行CRUD(增删改查)的操作。
而这些操作基本是相似的，只是表结构不同而已。

为此,farseer-go为我们定义了一些通用的CRUD操作，省去我们重复去实现，先来看下接口：

```go
package repository

import "github.com/farseer-go/collections"

// IRepository 通用的仓储接口，实现常用的CURD
type IRepository[TDomainObject any] interface {
	// ToEntity 查询实体
	ToEntity(id any) TDomainObject
	// Add 添加实体
	Add(entity TDomainObject)
	// ToList 获取所有列表
	ToList() collections.List[TDomainObject]
	// ToPageList 分页列表
	ToPageList(pageSize, pageIndex int) collections.PageList[TDomainObject]
	// Count 数量
	Count() int64
}
```

可以看到这个`IRepository`接口是一个泛型，其类型是聚合类型。

一般来说，简单的业务，一般会有聚合（Domain Object）、Model（Persistent Object）、以及insert/select/update/delete/count等操作。

我们看下如何使用这个接口，首先在您的domain层定义仓储接口：
```go
package product

import (
	"github.com/farseer-go/collections"
	"github.com/farseer-go/data/repository"
)

// Repository 商品仓储
type Repository interface {
	// IRepository 通用的仓储接口
	repository.IRepository[DomainObject]
	// ToPageListByCateId 获取商品列表
	ToPageListByCateId(cateId, pageSize, pageIndex int) collections.PageList[DomainObject]
}
```

使用匿名的方式来继承`IRepository`接口。同时如果通用的接口提供的方法不足以满足业务需求，则自己可以定义额外的方法，如：`ToPageListByCateId`

接下来，需要注册这个`IRepository`接口：
```go
// MysqlContextIns 初始化数据库上下文
var MysqlContextIns *MysqlContext

// MysqlContext 数据库上下文
type MysqlContext struct {
	// 定义数据库表 产品 映射TableSet
	Product data.TableSet[model.ProductPO] `data:"name=farseer_go_product"`
}

// InitMysqlContext 初始化上下文
func InitMysqlContext() {
	MysqlContextIns = data.NewContext[MysqlContext]("default", true)
	// 注册通用的仓储接口
	repository.RegisterRepository[product.DomainObject](MysqlContextIns.Product)
}
```

在初始化上下文的时候，调用了`repository.RegisterRepository`来注册这个接口，泛型传入的是`product.DomainObject`是领域对象。实参传入的是TableSet类型，用于绑定。

这样我们原先定义的`Repository`接口，就有了通用的CRUD功能了。

最后看一下仓储实现的定义：
```go
// InitProduct 注册商品仓储 ioc product.Repository
func InitProduct() {
	container.Register(func() product.Repository {
		return &ProductRepository{}
	})
}

type ProductRepository struct {
	repository.IRepository[product.DomainObject]
}

func (p *ProductRepository) ToPageListByCateId(cateId, pageSize, pageIndex int) collections.PageList[product.DomainObject] {
	// 需要筛选商品分类ID
	lstProduct := context.MysqlContextIns.Product.
		Select("Id", "Caption", "ImgSrc", "Price").
		WhereIgnoreLessZero("cate_id = ?", cateId).
		ToPageList(pageSize, pageIndex)

	// po 转 do
	var lst collections.PageList[product.DomainObject]
	lstProduct.MapToPageList(&lst)
	return lst
}
```

这样在仓储实现中，只需要手动实现业务中额外自定义的方法，其余通用的CRUD则不用再自己手动实现了。

最后看下在应用层的调用：
```go
// ToEntity 查看商品详细信息
// repository通过container自动注入实现进来
func ToEntity(productId int64, productRepository product.Repository) DTO { //
	do := productRepository.ToEntity(productId)
    return mapper.Single[DTO](do)
}
```
这里调用了productRepository实例的`ToEntity`方法，这个方法我们并没有去实现，它其实就是`IRepository`接口提供的，省去了我们手动实现。