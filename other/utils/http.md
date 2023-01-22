# http客户端
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/utils)

> 包：`"github.com/farseer-go/utils/http"`

## `NewClient` 创建http客户端
```go
_, _ = NewClient("https://www.fsgit.cc").Body(nil).Head(nil).Post()
```
## `Head` 设置头部
```go
// Head 设置头部
// head：头部
func (receiver *client) Head(head map[string]any) *client
```
## `HeadAdd` 添加头部
```go
// HeadAdd 添加头部
// key：Key
// value：Value
func (receiver *client) HeadAdd(key string, value any) *client
```
## `Body` 设置Body
```go
// Body 设置Body
// body：提交的内容
func (receiver *client) Body(body any) *client
```
## `SetJsonType` 设置application/json
```go
// SetJsonType 设置application/json
func (receiver *client) SetJsonType() *client
```
## `SetFormType` 设置application/x-www-form-urlencoded
```go
// SetFormType 设置application/x-www-form-urlencoded
func (receiver *client) SetFormType() *client
```
## `Timeout` 设置超时
```go
// Timeout 设置超时
// requestTimeout：超时时间（ms）
func (receiver *client) Timeout(requestTimeout int) *client
```
## `Post` POST请求
```go
// Post POST请求
func (receiver *client) Post() (string, error)
```
## `Get` GET方法请求
```go
// Get GET方法请求
func (receiver *client) Get() (string, error)
```
## `GetUnmarshal` GET方法请求，并反序列成对象
```go
// GetUnmarshal GET方法请求，并反序列成对象
func (receiver *client) GetUnmarshal(val any) error 
```
## `Put` PUT方法请求
```go
// Put PUT方法请求
func (receiver *client) Put() (string, error)
```
## `PutUnmarshal` PUT方法请求，并反序列成对象
```go
// PutUnmarshal PUT方法请求，并反序列成对象
func (receiver *client) PutUnmarshal(val any) error
```