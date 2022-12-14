# file文件管理
> 包：`"github.com/farseer-go/utils/file"`
## `CopyFolder` 复制整个文件夹
```go
// 复制整个文件夹
// srcPath 要复制的原目录路径
// destPath 复制到位置的目录路径
func CopyFolder(srcPath string, destPath string)
```
## `CopyFile` 复制文件
```go
// 复制文件
// srcName 复制的原文件
// destName 复制到目标位置（需带上文件名）
func CopyFile(srcName string, destName string)
```
## `CreateDir766` 创建所有目录，权限为766
```go
// CreateDir766 创建所有目录，权限为766
// path：目录
func CreateDir766(path string)
```
## `CreateDir` 创建所有目录
```go
// CreateDir 创建所有目录
// path：目录
// perm：目录权限
func CreateDir(path string, perm os.FileMode)
```
## `Delete` 删除文件
```go
// Delete 删除文件
// filePath：文件路径
func Delete(filePath string)
```
## `GetFiles` 读取指定目录下的文件
```go
// GetFiles 读取指定目录下的文件
// path：目录路径
// searchPattern：匹配文件名要包含的名称，搜索全部，传入""即可
// searchSubDir：是否要搜索子目录
func GetFiles(path string, searchPattern string, searchSubDir bool) []string
```
## `ClearFile` 清空目录下的所有文件（但不删除path目录本身）
```go
// ClearFile 清空目录下的所有文件（但不删除path目录本身）
// path：目录路径
func ClearFile(path string)
```
## `IsExists` 判断路径是否存在
```go
// IsExists 判断路径是否存在
// path：目录路径
func IsExists(path string) bool
```
## `ReadString` 读文件内容
```go
// ReadString 读文件内容
// filePath：文件路径
func ReadString(filePath string) string
```
## `ReadAllLines` 读文件内容，按行返回数组
```go
// ReadAllLines 读文件内容，按行返回数组
// filePath：文件路径
func ReadAllLines(filePath string) []string
```
## `WriteString` 写入文件
```go
// WriteString 写入文件
// filePath：文件路径
// content：文件内容
func WriteString(filePath string, content string)
```
## `AppendString` 追加文件
```go
// AppendString 追加文件
// filePath：文件路径
// content：文件内容
func AppendString(filePath string, content string)
```
## `AppendLine` 换行追加文件
```go
// AppendLine 换行追加文件
// filePath：文件路径
// content：文件内容
func AppendLine(filePath string, content string)
```
## `AppendAllLine` 换行追加文件
```go
// AppendAllLine 换行追加文件
// filePath：文件路径
// contents：文件内容
func AppendAllLine(filePath string, contents []string)
```