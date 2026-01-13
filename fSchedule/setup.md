# 安装
安装前准备，需要自行准备以下服务：
1. `mysql`（已测：mysql8.0.28），并创建好库
2. `redis`（已测：6.2.6）

> fSchedule运行时，会自动创建表
## 1、docker（推荐）
```shell
docker run --name fschedule -p 8886:8886 -d \
-e Database_default="DataType=mysql,PoolMaxSize=5,PoolMinSize=1,ConnectionString=root:123456@tcp(127.0.0.1:3306)/fschedule?charset=utf8&parseTime=True&loc=Local" \
-e Redis_default="Server=127.0.0.1:6379,DB=14,Password=123456,ConnectTimeout=600000,SyncTimeout=10000,ResponseTimeout=10000" \
steden88/fschedule:latest
```

**`环境变量说明`**
* `Database_default`：数据库配置
* `Redis_default`：Redis配置
* `FSchedule_Server_Token`: 鉴权token（默认空）
* `FSchedule_DataSyncTime`: 多少秒同步一次任务组数据到数据库（单位秒，默认60）
* `FSchedule_ReservedTaskCount`: 保留多少条已完成的任务数据（0不清理，默认60）

## 2、集群部署
```shell
docker run --name fschedule1 -p 80:8886 -d \
-e Database_default="DataType=mysql,PoolMaxSize=5,PoolMinSize=1,ConnectionString=root:123456@tcp(127.0.0.1:3306)/fschedule?charset=utf8&parseTime=True&loc=Local" \
-e Redis_default="Server=127.0.0.1:6379,DB=14,Password=123456,ConnectTimeout=600000,SyncTimeout=10000,ResponseTimeout=10000" \
steden88/fschedule:latest

docker run --name fschedule2 -p 81:8886 -d \
-e Database_default="DataType=mysql,PoolMaxSize=5,PoolMinSize=1,ConnectionString=root:123456@tcp(127.0.0.1:3306)/fschedule?charset=utf8&parseTime=True&loc=Local" \
-e Redis_default="Server=127.0.0.1:6379,DB=14,Password=123456,ConnectTimeout=600000,SyncTimeout=10000,ResponseTimeout=10000" \
steden88/fschedule:latest

docker run --name fschedule3 -p 82:8886 -d \
-e Database_default="DataType=mysql,PoolMaxSize=5,PoolMinSize=1,ConnectionString=root:123456@tcp(127.0.0.1:3306)/fschedule?charset=utf8&parseTime=True&loc=Local" \
-e Redis_default="Server=127.0.0.1:6379,DB=14,Password=123456,ConnectTimeout=600000,SyncTimeout=10000,ResponseTimeout=10000" \
steden88/fschedule:latest
```
> 多个节点运行，不需要特别配置，与单节点运行是一样的。

## 3、linux
需要`go 1.20+`
```shell
git clone https://github.com/FSchedule/FSchedule
cd FSchedule
# 编译应用
GOOS=linux GOARCH=amd64 CGO_ENABLED=0 go build -o fschedule-server -ldflags="-w -s" .
chmod +x fschedule-server
./fschedule-server
```

> 在应用程序根目录中有`farseer.yaml`配置文件，配置方式参考环境变量说明

## 4、mac
需要`go 1.20+`
```shell
git clone https://github.com/FSchedule/FSchedule
cd FSchedule
# 编译应用
GOOS=darwin GOARCH=amd64 CGO_ENABLED=0 go build -o fschedule-server -ldflags="-w -s" .
chmod +x fschedule-server
./fschedule-server
```
> 在应用程序根目录中有`farseer.yaml`配置文件，配置方式参考环境变量说明