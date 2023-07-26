# 错误信息

很多时候，我们的API接口返回的是model对象：
```go
func Check(clientId int64) ResourceVO {
	return getResource()
}
```
但在业务上，如果请求的参数是非法的，又或者登陆的时候账号密码错误，我们需要返回一个错误信息提示给前端。

这个时候就可以使用`exception.ThrowWebException`异常机制来实现（中间件有这个异常捕获）

```go
// Check 检查客户端存活
func Check(clientId int64) ResourceVO {
	if clientId != defaultClient.ClientId {
		exception.ThrowWebException(403, "客户端ID不一致")
	}
	return getResource()
}
```

结果：
```json
{
    "Status": false,
    "StatusCode": 403,
    "StatusMessage": "客户端ID不一致", 
    "Data": null
}
```