# 配置
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/data)

_./farseer.yaml_
```yaml
Database:
  fops: "DataType=MySql,PoolMaxSize=50,PoolMinSize=1,ConnectionString=root:123456@tcp(mysql:3306)/fops?charset=utf8&parseTime=True&loc=Local"
```

数据库的配置，根节点`Database`是框架定义的，如果你的应用需要多种数据库配置，可以自行添加：

_./farseer.yaml_
```yaml
Database:
  db1: "DataType=MySql,PoolMaxSize=50,PoolMinSize=1,ConnectionString=root:123456@tcp(mysql:3306)/fops?charset=utf8&parseTime=True&loc=Local"
  db2: "DataType=MySql,PoolMaxSize=50,PoolMinSize=1,ConnectionString=root:123456@tcp(mysql:3306)/fops?charset=utf8&parseTime=True&loc=Local"
  db3: "DataType=MySql,PoolMaxSize=50,PoolMinSize=1,ConnectionString=root:123456@tcp(mysql:3306)/fops?charset=utf8&parseTime=True&loc=Local"
```

`db1、db2、db3`允许自行定义，在使用data组件时，需要传入这个`db1、db2、db3`名称

连接字符串，由key=value组成，多个间用"`,`"分隔。

## 1、DataType
数据库类型（不区分大小写）：
- `mysql`
  - 是data组件默认支持的驱动，不需要您额外依赖。
- `postgresql`
  - 需要依赖：`github.com/farseer-go/data/driver/postgres`包，模块名：`data_postgres.Module`
- `sqlite`
  - 需要依赖：`github.com/farseer-go/data/driver/sqlite`包，模块名：`data_sqlite.Module`
- `sqlserver`
  - 需要依赖：`github.com/farseer-go/data/driver/sqlserver`包，模块名：`data_sqlserver.Module`
- `clickhouse`
  - 需要依赖：`github.com/farseer-go/data/driver/clickhouse`包，模块名：`data_clickhouse.Module`

?> 如果要支持mysql以外的数据库。需要将原本依赖data.Module模块改成上面列出来的模块名。比如下面依赖clickhouse模块：
```go
package infrastructure

import (
  "github.com/farseer-go/data/driver/clickhouse"
  "github.com/farseer-go/fs/modules"
)

type Module struct {
}
func (module Module) DependsModule() []modules.FarseerModule {
	// 这些模块都是farseer-go内置的模块
	return []modules.FarseerModule{data_clickhouse.Module{}}
}
```
## 2、PoolMaxSize
连接池最大数量

## 3、PoolMinSize
连接池最小数量

## 4、ConnectionString
### 4.1、mysql
连接字符串：**user:pass@tcp(127.0.0.1:3306)/dbname?charset=utf8mb4&parseTime=True&loc=Local**

?> 参考 https://github.com/go-sql-driver/mysql#dsn-data-source-name 获取详情
### 4.2、PostgreSQL
连接字符串：**user=user password=pass dbname=dbname port=9920 sslmode=disable TimeZone=Asia/Shanghai**

### 4.3、sqlite
连接字符串：**xxx.db**

?> 注意： 您也可以使用 file::memory:?cache=shared 替代文件路径。 这会告诉 SQLite 在系统内存中使用一个临时数据库

### 4.4、SQL Server
连接字符串：**sqlserver://user:pass@localhost:9930?database=dbname**

### 4.5、ClickHouse
连接字符串：**clickhouse://user:123456@127.0.0.1:9942/dbname?dial_timeout=10s&read_timeout=20s**

?> 参考：https://github.com/go-gorm/clickhouse