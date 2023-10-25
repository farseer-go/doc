# DbContext 上下文
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/data)

> 包：`"github.com/farseer-go/data"`
>
> 模块：`data.Module`

上下文可以理解为一个数据库的逻辑库，由应用自身去定义，然后在上下文中定义各个表，比如：

_mysqlContext.go_
```go
type MysqlContext struct { // MysqlContext是你要实现的代码
	Admin         data.TableSet[model.AdminPO]         `data:"name=admin;migrate"` // data.name 表名
	Build         data.TableSet[model.BuildPO]         `data:"name=build;migrate"`
	Cluster       data.TableSet[model.ClusterPO]       `data:"name=cluster;migrate"`
	DockerfileTpl data.TableSet[model.DockerfileTplPO] `data:"name=dockerfile_tpl;migrate"`
	DockerHub     data.TableSet[model.DockerHubPO]     `data:"name=docker_hub;migrate"`
	Git           data.TableSet[model.GitPO]           `data:"name=basic_git;migrate"`
	Project       data.TableSet[model.ProjectPO]       `data:"name=basic_project;migrate"`
	ProjectGroup  data.TableSet[model.ProjectGroupPO]  `data:"name=basic_project_group;migrate"`
	YamlTpl       data.TableSet[model.YamlTplPO]       `data:"name=k8s_yaml_tpl;migrate=InnoDB"`
}
```
每个model，由`data.TableSet`包裹，`data.TableSet`是用来操作数据库表的相关方法。

- `data:"name=admin"`用来标记数据库的表名
- `data:"migrate=InnoDB"`用来标记数据库的表引擎，对于InnoDB引擎，可以直接用："migrate"

?> 标记migrate之后，意味着该表会被自动创建。 

## 1、Model层
在应用中，我们会定义一系列的model，用来匹配数据库表结构，比如：

_model.AdminPO.go_
```go
type AdminPO struct {
	// 主键，自增ID
    Id int `gorm:"primaryKey;autoIncrement;comment:ID"`
    // 管理员名称
    UserName string `gorm:"size:32;not null;index:idx_user_name;default:'';comment:管理员名称"`
    // 管理员密码
    UserPwd string `gorm:"size:32;not null;default:'';comment:管理员密码"`
	// 账号类型
    AccountType accountType.Enum `gorm:"type:tinyint;not null;default:0;comment:账号类型"`
	// 允许登陆
    AllowLogin bool `gorm:"size:1;not null;default:0;comment:允许登陆"`
	// 分类ID
    CateIds []int `gorm:"size:32;not null;json;default:'[]';comment:分类ID"`
	// 创建时间
    CreateAt time.Time `gorm:"type:timestamp;size:6;not null;default:CURRENT_TIMESTAMP;comment:创建时间"`
}
```

## 2、data.NewContext初始化上下文

初始化，非常简单，只需要调用`data.NewContext`函数，并传入`dbName`即可：
```go
var MysqlContextIns *MysqlContext
// 在模块中调用这个函数来执行初始化上下文
func InitMysqlContext() {
    MysqlContextIns = data.NewContext[MysqlContext]("fops")
}
```

## 2.1 参数说明：
- fops：数据库配置名称，对应./farseer.yaml 中的 Database节点
- true：传入true表示自动创建表，以此实现code first模式

## 3、获取原生ORM对象
有时候需要在不绑定model时使用原生orm对象，可以通过在上下文中内嵌`data.IInternalContext`接口实现：
```go
// MysqlContext 数据库上下文
type MysqlContext struct {
	// 手动使用事务时必须定义
	core.ITransaction
	// 获取原生ORM框架（不使用TableSet或DomainSet）
	data.IInternalContext
	// 定义数据库表 订单 映射TableSet
	Order data.DomainSet[model.OrderPO, order.DomainObject] `data:"name=farseer_go_order"`
	// 定义数据库表 商品分类 映射TableSet
	ProCate data.DomainSet[model.ProCatePO, procate.DomainObject] `data:"name=farseer_go_procate"`
	// 定义数据库表 产品 映射TableSet
	Product data.DomainSet[model.ProductPO, product.DomainObject] `data:"name=farseer_go_product"`
}
```
调用示例：
```go
    MysqlContextIns := data.NewContext[MysqlContext]("default")
	MysqlContextIns.Original().xxx
```
`Original()`函数将返回原生的orm对象