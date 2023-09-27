# 事务操作

在data组件中，事务操作有两种方式：

## 接口方式（推荐）
这里我们使用到`core.ITransaction`接口，在data组件中已自动帮我们注册好这个接口的实例，我们只需要获取就可以了

```go
transaction := container.Resolve[core.ITransaction]("default")
// 开启数据库事务
transaction.Transaction(func() {
    // 发布下单事件
    _ = buyOrderEvent.Publish(&productDO)
})
```
配合`webapi`组件，可以更简单：
```go
func Buy(productId int64, transaction core.ITransaction) {
    // 开启数据库事务
    transaction.Transaction(func() {
        // 发布下单事件
        _ = buyOrderEvent.Publish(&productDO)
    })
}
```

使用接口方式可以看到`与数据库仓储完全解耦`了，这里并没有对data组件的依赖，也没有对`数据库的直接依赖`。

在`Transaction()`函数中的数据库操作，都会自动使用到事务。

## 手动开启

首先定义一个`context.MysqlContextIns`数据库上下文：
```go
// MysqlContextIns 初始化数据库上下文
var MysqlContextIns *MysqlContext

// MysqlContext 数据库上下文
type MysqlContext struct {
	// 使用事务时必须定义
	core.ITransaction
	// 定义数据库表 订单 映射TableSet
	Order data.TableSet[model.OrderPO] `data:"name=farseer_go_order"`
}

// InitMysqlContext 初始化上下文
func InitMysqlContext() {
	MysqlContextIns = data.NewContext[MysqlContext]("default")
}
```

然后在需要使用事务的SQL代码中使用`Transaction()`函数
```go
context.MysqlContextIns.Transaction(func() {
    context.MysqlContextIns.Order.Insert(&po)
})
```
通常这段代码是在仓储层写的。因为涉及到直接对表的操作。

也可以手动Begin/Commit:
```go
context.MysqlContextIns.Begin()
context.MysqlContextIns.Order.Insert(&po)
context.MysqlContextIns.Commit()
```

## 总结
?>两种事务开启，我推荐第一种接口方式，这样可以让我们的事务控制放在应用层，这也符合DDD的思想。