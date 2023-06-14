# CURD操作
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/data)
## 什么是TableSet
在上下文的时候，我们都会用data.TableSet做一层包裹：
```go
type MysqlContext struct {
    Admin data.TableSet[model.AdminPO] `data:"name=admin"`
}
```
在调用了`data.NewContext`方法后，会查找结构中`data.TableSet类型`的字段并执行`初始化操作`

在所有的CURD操作，都是由TableSet提供的方法，接下来详细介绍每个方法

## 1、SetTableName 设置表名
SetTableName允许在运行时动态设置表名，在数据量大时一般都会采用`分表的方式`来缓解数据压力。

因此，我们可以使用`SetTableName方法`来达到分表的目地。

_SetTableName定义_
```go
func (table *TableSet[Table]) SetTableName(tableName string) *TableSet[Table]
```

例如：
```go
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.SetTableName("admin_2022_12").ToList()
```

这样我们就将表名设置为：`admin_2022_12`。

## 2、Select 字段筛选
```go
func (table *TableSet[Table]) Select(query any, args ...any) *TableSet[Table]
```
支持在查找数据时或修改数据时，筛选部份字段数据。

这里注意下返回值`*TableSet[Table]`，意味着它是一个`链式操作`，在`TableSet`提供的方法中，我们大量的使用这种方式。

_只显示Id、UserName字段_
```go
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.Select("Id", "UserName").ToList()
```

## 3、Where 筛选条件
```go
func (table *TableSet[Table]) Where(query any, args ...any) *TableSet[Table]
```
_匹配用户名、密码相同的用户_
```go
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.Where("user_name = ? && user_pwd = ?", userName, pwd).ToEntity()
```
?> `?`是一个占位符，表示`字段的参数化`

## 4、Order 排序
```go
func (table *TableSet[Table]) Order(value any) *TableSet[Table]
func (table *TableSet[Table]) Desc(fieldName string) *TableSet[Table]
func (table *TableSet[Table]) Asc(fieldName string) *TableSet[Table]
```
提供了三种排序方式，其中`Desc、Asc`从字面意思可以知道，其是对`字段的统一排序`，比如：
```go
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.Select("Id", "UserName").Where("id > ?", 1).Desc("id").ToList()
repository.Admin.Select("Id", "UserName").Where("id > ?", 1).Asc("id").ToList()
```
或者：
```go
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.Select("Id", "UserName").Where("id > ?", 1).Order("id desc").ToList()
```

当然你也可以`传入多个字段`，字段之间用`,`分隔

## 5、Limit 限制数量
```go
func (table *TableSet[Table]) Limit(limit int) *TableSet[Table] 
```
_只显示前5条_
```go
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.Select("Id", "UserName").Where("id > ?", 1).Desc("id").Limit(5).ToList()
```

## 6、ToList、ToArray获取数据集合
```go
func (table *TableSet[Table]) ToList() collections.List[Table]
func (table *TableSet[Table]) ToArray() []Table
```
_获取所有数据_
```go
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.Select("Id", "UserName").ToList()
```

?> 推荐使用`ToList()`，[collections.List](/dataStructure/list.md)是框架提供的数据集合，拥有更高级的数据操作能力。

## 7、ToPageList分页
```go
func (table *TableSet[Table]) ToPageList(pageSize int, pageIndex int) collections.PageList[Table]
```
- pageSize：分页大小
- pageIndex：当前页索引

?> [collections.PageList](/dataStructure/pageList.md)是框架提供的数据集合

## 8、ToEntity获取单个对象
```go
func (table *TableSet[Table]) ToEntity() Table
```
获取单条记录时，也可以配合Select方法来做脱敏字段。
```go
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.Where("user_name = ? && user_pwd = ?", userName, pwd).Select("Id", "UserName").ToEntity()
```
比如这里只显示Id、UserName两个字段
## 9、Count获取数量
```go
func (table *TableSet[Table]) Count() int64
```
## 10、IsExists是否存在记录
```go
func (table *TableSet[Table]) IsExists() bool
```
## 11、Insert插入记录
```go
func (table *TableSet[Table]) Insert(po *Table)
```

```go
po:= model.AdminPO{
    UserName:    "steden",
    UserPwd:     "123456",
}
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.Insert(&po)
```
插入记录的方法很简单，这里主要讲一下特殊的数据类型时的处理办法

除了常用的string、int、bool字段类型外，我们还会存一些例如：json、字典、数据列表这种

得益于go语言的隐式接口，让我们扩展起来更加的简单：
```go
func (receiver Dictionary[TKey, TValue]) Value() (driver.Value, error) 
func (receiver *Dictionary[TKey, TValue]) Scan(val any) error
```
在你`自定义的类型`，只要实现了`Value、Scan`方法，告诉框架该`如何持久化数据`，便能正确的存储到数据库中

!> 在`collections`包中的所有数据类型（List、Dictionary、ListAny、PageList），我们都已实现好了。

## 12、Update修改对象
```go
func (table *TableSet[Table]) Update(po Table) int64
```
_修改id=3的数据_
```go
po:= model.AdminPO{
    UserName:    "steden",
    UserPwd:     "123456",
}
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.Where("Id = ?", 3).Update(po)
```
!> 多model有多个字段，而`只想修改指定的几个字段`时，可以搭配`Select()`方法来做`字段限制`

## 13、UpdateValue修改一个字段
```go
func (table *TableSet[Table]) UpdateValue(column string, value any)
```
_修改密码_
```go
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.Where("Id = ?", 3).UpdateValue("user_pwd","888888")
```

## 14、Delete删除记录
```go
func (table *TableSet[Table]) Delete() int64
```
_删除指定ID的数据_
```go
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.Where("Id = ?", id).Delete()
```
## 15、GetXXX获取单个字段值
```go
func (table *TableSet[Table]) GetString(fieldName string) string
func (table *TableSet[Table]) GetInt(fieldName string) int
func (table *TableSet[Table]) GetLong(fieldName string) int64
func (table *TableSet[Table]) GetBool(fieldName string) bool
func (table *TableSet[Table]) GetFloat32(fieldName string) float32
func (table *TableSet[Table]) GetFloat64(fieldName string) float64
```

_获取用户名_
```go
repository:= data.NewContext[MysqlContext]("fops")
repository.Admin.Where("Id = ?", id).GetString("user_name")
```