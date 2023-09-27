# 多租户
数据库连接字符串配置到farseer.yaml通常是因为数据库数量是固定的。

但在多租户场景下，数据库的连接字符串是存到表记录的。这个时候要注册数据库的实例的方法为：

首先你要在farseer.yaml中定义固定的库，用来获取多租户的连接字符串。
```yaml
Database:
  default: "DataType=mysql,PoolMaxSize=50,PoolMinSize=1,ConnectionString=root:123456@tcp(127.0.0.1:3306)/company?charset=utf8&parseTime=True&loc=Local"
```
跟之前一样，有一个对应的上下文：
```go
package context
// MysqlContext 初始化数据库上下文
var MysqlContext *mysqlContext
// mysqlContext 数据库上下文
type mysqlContext struct {
	// 手动使用事务时必须定义
	core.ITransaction
	// 获取原生ORM框架（不使用TableSet或DomainSet）
	data.IInternalContext
	// company 公司表
	Company data.DomainSet[model.CompanyPO, company.DomainObject] `data:"name=company"`
}
// InitMysqlContext 初始化上下文
func InitMysqlContext() {
	MysqlContext = data.NewContext[mysqlContext]("default")
}
```
然后在基础设施层的PostInitialize环节中，通过读取配置记录表，然后进行动态注册：
```go
package infrastructure
type Module struct { }
func (module Module) DependsModule() []modules.FarseerModule {
	return []modules.FarseerModule{data.Module{}}
}

func (module Module) PostInitialize() {
	// 初始化仓储
	context.InitMysqlContext()

	// 从公司表中读取租户信息，然后注册数据库上下文
	container.Resolve[company.Repository]().ToList().Where(func(item company.DomainObject) bool {
		return item.Enable
	}).Foreach(func(item *company.DomainObject) {
		// 租户ID
		strCompanyId := parse.ToString(item.Id)
		// 注册数据库
		data.RegisterInternalContext(strCompanyId, item.Conn.Db)
		context.InitMultiMysqlContext(item.Id)
	})
}
```
示例中可以看到有一张`company表`，其中`item.Conn.Db`字段：保存租户的数据库配置信息。

`item.Conn.Db`字段内容："DataType=mysql,ConnectionString=root:123456@tcp(127.0.0.1:3306)/yourDbName?charset=utf8&parseTime=True&loc=Local"

`data.RegisterInternalContext(strCompanyId, item.Conn.Db)`就是本方案的重点。向data组件注册实例（并且也实现了这个动态库的健康检查）

最后就是多租户的上下文：
```go
package context
var mapMysqlContext = make(map[int]*multiMysqlContext)
// mysqlContext 数据库上下文 实际业务
type multiMysqlContext struct {
	// 手动使用事务时必须定义
	core.ITransaction
	// 获取原生ORM框架（不使用TableSet或DomainSet）
	data.IInternalContext
	// 系统通知
	News data.DomainSet[model.NewsPO, news.DomainObject] `data:"name=news"`
	// 账号
	account data.DomainSet[model.AccountPO, account.DomainObject] `data:"name=account"`
}

// InitMultiMysqlContext 初始化上下文
func InitMultiMysqlContext(companyId int) {
	mapMysqlContext[companyId] = data.NewContext[multiMysqlContext](parse.ToString(companyId), true)
}

func MultiMysqlContext(companyId int) *multiMysqlContext {
	return mapMysqlContext[companyId]
}

```