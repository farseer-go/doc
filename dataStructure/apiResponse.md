# apiResponse 类型
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/fs)
> 包：`"github.com/farseer-go/fs/core"`

apiResponse类型，一般用于api服务返回类型的包裹结构

## 1、数据结构定义
```go
type ApiResponse[TData any] struct {
    // 操作是否成功
    Status bool
    // 返回状态代码
    StatusCode int
    // 返回消息内容
    StatusMessage string
    // 不同接口返回的值
    Data TData
}
```

序列化成json后：
```json
{
    "Status": true,
    "StatusCode": 200,
    "StatusMessage": "成功",
    "Data": null
}
```

对于api服务来说，这几个字段都是常用的，因此farseer-go框架对这一数据类型做了集成。

可以跳到webapi组件中查看，在中间件中支持自动设置：[webapi中间件](/web/webapi/middleware.md?id=_6%e3%80%81apiresponse)

## 2、常用的默认类型
```go
type ApiResponseInt ApiResponse[int]
type ApiResponseLong ApiResponse[int64]
type ApiResponseString ApiResponse[string]
```

## 3、SetData
```go
apiResponse:= core.ApiResponse[string]{
    Status:true,
    StatusCode: 200,
    StatusMessage: "成功",
}

apiResponse.SetData("123456")
apiResponse.ToJson()
```
_运行结果：_
```json
{
    "Status": true,
    "StatusCode": 200,
    "StatusMessage": "成功",
    "Data": "123456"
}
```

## 4、ToJson
将对象转换成Json string

## 5、ToBytes
将对象转换成Json []byte

## 6、Success
```go
core.Success("成功",123).ToJson()
```
_运行结果：_
```json
{
    "Status": true,
    "StatusCode": 200,
    "StatusMessage": "成功",
    "Data": 123
}
```

## 7、Error
```go
core.Error[any]("失败" ,500).ToJson()
```
_运行结果：_
```json
{
    "Status": false,
    "StatusCode": 500,
    "StatusMessage": "失败",
    "Data": null
}
```

## 8、Error403
```go
core.Error403[any]("失败").ToJson()
```
_运行结果：_
```json
{
    "Status": false,
    "StatusCode": 403,
    "StatusMessage": "失败", 
    "Data": null
}
```

