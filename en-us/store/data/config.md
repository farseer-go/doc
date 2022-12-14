# 配置

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

## DataType
数据库类型（不区分大小写）：
- `mysql`
- `postgresql`
- `sqlite`
- `sqlserver`

## PoolMaxSize
连接池最大数量

## PoolMinSize
连接池最小数量

## ConnectionString
### mysql
`user`:`pass`@tcp(`127.0.0.1:3306`)/`dbname`?charset=utf8mb4&parseTime=True&loc=Local

?> 参考 https://github.com/go-sql-driver/mysql#dsn-data-source-name 获取详情
### PostgreSQL
user=`user` password=`pass` dbname=`dbname` port=`9920` sslmode=disable TimeZone=Asia/Shanghai

### sqlite
"xxx.db"

?> 注意： 您也可以使用 file::memory:?cache=shared 替代文件路径。 这会告诉 SQLite 在系统内存中使用一个临时数据库

### SQL Server
sqlserver://`user`:`pass`@`localhost:9930`?database=`dbname`