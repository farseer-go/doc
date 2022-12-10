# parse类型转换
> 包：`"github.com/farseer-go/fs/parse"`

得益于在`go 1.18`以后支持了函数泛型，使得类型转换可以更加的优雅了
```go
func Convert[T any](source any, defVal T) T
```

?> Convert`接受一个泛型`，`source参数`为转换前的值，`defVal`为转换`失败时的默认值`，`T`为要`转换后的类型`。

## 支持的类型互转
- `int8`
- `int16`
- `int32`
- `int64`
- `int`
- `uint8`
- `uint16`
- `uint32`
- `uint64`
- `uint`
- `bool`
- `float32`
- `float64`
- `string`

## 转换示例

```go
// 我们知道，在数字与bool之间，我们一般规定0=false、1=true
// 这里result = false
result := parse.Convert(0, false)

// result = "1"
result := parse.Convert(1, "")

// result = int64(1)
result := parse.Convert(1, int64(0))

// result = true
result := parse.Convert("true", false)

// result = 123
result := parse.Convert("123", 0)
```

?> 这让我们在日常处理这些`基本类型之间的转换`时，会容易很多

## string转[]string
```go
// result = [0]="a"，[0]="b"，[0]="c"，[0]="d"
result := parse.Convert("a,b,c,d", []string{})
```
类似strings.Join操作

## 支持reflect.Type转换
```go
func ConvertValue(source any, defValType reflect.Type) reflect.Value
```
这在反射时特别有用，比如：
```go
headerVal := httpContext.Header.GetValue(headerName)
headerFieldType := headerFieldVal.Type()

headerValue := parse.ConvertValue(headerVal, headerFieldType)
headerFieldVal.Set(headerValue)
```