# 安装
## Docker运行（推荐）
```shell
docker kill fschedule
docker rm fschedule
docker run --name fschedule -d \
-e WebApi_Url=":80" \
-e Database_default="DataType=mysql,PoolMaxSize=50,PoolMinSize=1,ConnectionString=root:123456@tcp(127.0.0.1:3306)/fschedule?charset=utf8&parseTime=True&loc=Local" \
-e Redis_default="Server=127.0.0.1:6379,DB=14,Password=123456,ConnectTimeout=600000,SyncTimeout=10000,ResponseTimeout=10000" \
steden88/fschedule:latest
docker logs fschedule
```
### 环境变量说明
* `Database_default`：数据库配置
* `Redis_default`：Redis配置
* `FSCHEDULE_SERVER_TOKEN`: 鉴权token（默认空）
* `FSCHEDULE_DATASYNCTIME`: 多少秒同步一次任务组数据到数据库（单位秒，默认60）
* `FSCHEDULE_RESERVEDTASKCOUNT`: 保留多少条已完成的任务数据（0不清理，默认60）