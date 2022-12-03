# data 数据库ORM组件
> 包：`"github.com/farseer-go/data"`
>
> 模块：`data.Module`

代码部份：
```go
func init() {
	// 注册仓储
	_ = container.Register(func() admin.Repository {
		return &adminRepository{
			data.Init[context.MysqlContext]("fops").Admin, // fops是数据库名称，定义在farseer.yaml的 Database.fops节点
		}
	})
}

type adminRepository struct {
	data.TableSet[model.AdminPO]
}

// IsExists 管理员是否存在
func (repository adminRepository) IsExists(adminName string) bool {
	return repository.Where("UserName = ?", adminName).IsExists()
}

// IsExistsWithoutSelf 管理员是否存在
func (repository adminRepository) IsExistsWithoutSelf(adminName string, adminId int) bool {
	return repository.Where("UserName = ? and Id <> ?", adminName, adminId).IsExists()
}
```

## data.TableSet有哪些方法
```go
// Select 筛选字段
func (table TableSet[Table]) Select(query interface{}, args ...interface{}) TableSet[Table]

// Where 条件
func (table TableSet[Table]) Where(query interface{}, args ...interface{}) TableSet[Table]

// Order 排序
func (table TableSet[Table]) Order(value interface{}) TableSet[Table]

// Desc 倒序
func (table TableSet[Table]) Desc(fieldName string) TableSet[Table]

// Asc 正序
func (table TableSet[Table]) Asc(fieldName string) TableSet[Table]

// ToList 返回结果集
func (table TableSet[Table]) ToList() []Table

// ToPageList 返回分页结果集
func (table TableSet[Table]) ToPageList(pageSize int, pageIndex int) core.PageList[Table]

// ToEntity 返回单个对象
func (table TableSet[Table]) ToEntity() Table

// Count 返回表中的数量
func (table TableSet[Table]) Count() int64

// IsExists 是否存在记录
func (table TableSet[Table]) IsExists() bool

// Insert 新增记录
func (table TableSet[Table]) Insert(po *Table)

// Update 修改记录
func (table TableSet[Table]) Update(po Table) int64

// UpdateValue 修改单个字段
func (table TableSet[Table]) UpdateValue(column string, value interface{})

// Delete 删除记录
func (table TableSet[Table]) Delete() int64

// GetString 获取单条记录中的单个string类型字段值
func (table TableSet[Table]) GetString(fieldName string) string

// GetInt 获取单条记录中的单个int类型字段值
func (table TableSet[Table]) GetInt(fieldName string) int

// GetLong 获取单条记录中的单个int64类型字段值
func (table TableSet[Table]) GetLong(fieldName string) int64

// GetBool 获取单条记录中的单个bool类型字段值
func (table TableSet[Table]) GetBool(fieldName string) bool

// GetFloat32 获取单条记录中的单个float32类型字段值
func (table TableSet[Table]) GetFloat32(fieldName string) float32

// GetFloat64 获取单条记录中的单个float64类型字段值
func (table TableSet[Table]) GetFloat64(fieldName string) float64
```

如果返回的是`TableSet[Table]`类型，代表是链式调用。