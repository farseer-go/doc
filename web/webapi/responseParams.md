# 出参
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/webapi)

出参支持三种类型：
- **DTO模式**：当只有`1个struct`类型时，则会被转成json输出
- **1个基础类型**：当只有一个基础类型时（如：string、int），会被直接输出
- **多个出参**：会放到一个数组后以json输出

## 1、DTO模式
```go
func Hello3(pageSize int, pageIndex int) pageSizeRequest {
    return request.PageSizeRequest{
        PageSize:  pageSize,
        PageIndex: pageIndex,
    }
}
```
这时会把`pageSizeRequest`序列化成json后输出

_运行结果：_
```json
{
    "PageSize": 10,
    "PageIndex": 1
}
```

## 2、1个基础类型
```go
func Hello1(req request.PageSizeRequest) string {
    return fmt.Sprintf("hello world pageSize=%d，pageIndex=%d", req.PageSize, req.PageIndex)
}
```
会被直接输出

_运行结果：_
```text
"hello world pageSize=3，pageIndex=1"
```

## 3、多个出参
```go
func Hello4(pageSize int, pageIndex int) (int, int) {
    return pageSize, pageIndex
}
```
这时，会将多个返回值组成json数组

_运行结果：_
```json
[ 10, 1 ]
```