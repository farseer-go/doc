# exec执行shell
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/utils)

> 包：`"github.com/farseer-go/utils/exec"`

## `RunShell` 执行shell命令
```go
// RunShell 执行shell命令
// command：要执行的命令
// receiveOutput：输出流
// environment：环境变量
// workingDirectory：当前工作目录位置
// return：exit code
func RunShell(command string, receiveOutput chan string, environment map[string]string, workingDirectory string) int
```
## `RunShellContext` 执行shell命令
```go
// RunShellContext 执行shell命令
// command：要执行的命令
// receiveOutput：输出流
// environment：环境变量
// workingDirectory：当前工作目录位置
// return：exit code
func RunShellContext(command string, receiveOutput chan string, environment map[string]string, workingDirectory string, ctx context.Context) int
```