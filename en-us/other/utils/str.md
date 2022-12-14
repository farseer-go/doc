# str字符串处理
> 包：`"github.com/farseer-go/utils/str"`

## `CutRight` 裁剪末尾标签
```go
// CutRight 裁剪末尾标签
// str：要裁剪的原字符串
// lastTag：裁剪的字符串
func CutRight(str string, lastTag string) string
```

## `MapToStringList` 将map转成字符串数组
```go
// MapToStringList 将map转成字符串数组，map的kv转成：k=v格式
func MapToStringList[TKey comparable, TValue any](maps map[TKey]TValue) []string
```

## `ToDateTime` 将时间转换为yyyy-MM-dd HH:mm:ss
```go
// ToDateTime 将时间转换为yyyy-MM-dd HH:mm:ss
func ToDateTime(t time.Time) string
```