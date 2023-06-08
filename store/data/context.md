# DbContext 上下文
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/data)

> 包：`"github.com/farseer-go/data"`
>
> 模块：`data.Module`

事实上，data组件并没有上下文这个结构，而是由应用自身去定义，比如：

_mysqlContext.go_
```go
type mysqlContext struct { // MysqlContext是你要实现的代码
	Admin         data.TableSet[model.AdminPO]         `data:"name=admin"` // data.name 表名
	Build         data.TableSet[model.BuildPO]         `data:"name=build"`
	Cluster       data.TableSet[model.ClusterPO]       `data:"name=cluster"`
	DockerfileTpl data.TableSet[model.DockerfileTplPO] `data:"name=dockerfile_tpl"`
	DockerHub     data.TableSet[model.DockerHubPO]     `data:"name=docker_hub"`
	Git           data.TableSet[model.GitPO]           `data:"name=basic_git"`
	Project       data.TableSet[model.ProjectPO]       `data:"name=basic_project"`
	ProjectGroup  data.TableSet[model.ProjectGroupPO]  `data:"name=basic_project_group"`
	YamlTpl       data.TableSet[model.YamlTplPO]       `data:"name=k8s_yaml_tpl"`
}
```
每个model，由`data.TableSet`包裹，`data.TableSet`是用来操作数据库表的相关方法。

其中标签``data:"name=admin"``用来标记数据库的表名


## 1、Model层
在应用中，我们会定义一系列的model，用来匹配数据库表结构，比如：

_model.AdminPO.go_
```go
type AdminPO struct {
    Id int `gorm:"primaryKey"`  // 标记为主键
    // 管理员名称
    UserName string
    // 管理员密码
    UserPwd string
}
```

!> 注意：标签gorm是临时的，未来版本中会变更。

## 2、data.NewContext初始化上下文

_dbContext.go_
```go
func NewContext[TDbContext any](dbName string) *TDbContext
```
有两种方式，一种是使用IOC容器，一种是直接初始化：

### 2.1、注册到容器
```go
type adminRepository struct {
    data.TableSet[model.AdminPO] `data:"name=admin"`
}

func RegisterRepository() {
    // 注册项目组仓储
    container.Register(func() admin.Repository {
        return data.NewContext[adminRepository]("fops")
    })
}
```

`admin.Repository`是定义的`interface`，`adminRepository`实现了`admin.Repository`接口，这里先不看技术细节，具体的会在后面章节介绍

字符串`"fops"`就是在./farseer.yaml中 `Database.fops`节点定义的数据库配置

`container.Register`，在[container容器](/basic/container.md?id=注册单例)组件中我们介绍过，这是`注册单例`的方式。

在需要使用这个数据库上下文时，可以：
```go
container.Resolve[admin.Repository]()
```

我们就可以很完美的将数据库操作的`定义`与`实现`完全`解耦`。

### 2.2、直接初始化
直接初始化，非常简单，只需要调用`data.NewContext`函数，并传入`dbName`即可：
```go
data.NewContext[adminRepository]("fops")
```