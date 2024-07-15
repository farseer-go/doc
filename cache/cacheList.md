# 本地缓存

## 创建对象 
```go
type OrderEO struct {
	OrderNo string
    ...
}
cache := cacheMemory.NewCache[OrderEO]()
```

## 写入缓存（如果不存在）
```go
cache.SetIfNotExists("123456", func() *OrderEO {
    return &OrderEO{
        OrderNo: "123456",
    }
})
```

## 获取缓存
```go
OrderEO := cache.Get("123456")
```