# async异步并行
[中文文档](https://farseer-go.github.io/doc/)、[English Document](https://farseer-go.github.io/doc/#/en-us/)、[github Source](https://github.com/farseer-go/async)
> 包：`"github.com/farseer-go/async"`

![](https://img.shields.io/github/stars/farseer-go?style=social)
![](https://img.shields.io/github/license/farseer-go/async)
![](https://img.shields.io/github/go-mod/go-version/farseer-go/async)
![](https://img.shields.io/github/v/release/farseer-go/async)
[![codecov](https://img.shields.io/codecov/c/github/farseer-go/async)](https://codecov.io/gh/farseer-go/async)
![](https://img.shields.io/github/languages/code-size/farseer-go/async)
[![Build](https://github.com/farseer-go/async/actions/workflows/test.yml/badge.svg)](https://github.com/farseer-go/async/actions/workflows/test.yml)
![](https://goreportcard.com/badge/github.com/farseer-go/async)


## 1、概述
当我们需要并行执行部分函数且阻塞等待这些方法执行完成后再继续往下执行，
或者当需要异步并行执行部分方法，且执行完成后执行回调方法时，可使用async组件。

## 2、同步执行（阻塞）
本着farseer-go极简、优雅风格，使用async组件也是非常简单的：

```go
func Parallel(fns ...func())
```
- `fns`：执行方法（支持传入多个）

```go
func Add(fns ...func())
```
- `fns`：添加异步执行方法（支持传入多个）

```go
func Wait(fns ...func())
```
- 阻塞等待


_演示：_
```go
var count = 0
_ = Parallel(func() { count += 1}).Add(func() { count += 2 }).Wait() // 阻塞等待，直到两个函数执行完
count *= 2  // 由于阻塞，所以这里最后执行
// count = 6 // 最终结果
```

## 3、异步执行（不阻塞）

```go
func ContinueWith(callbacks ...func())
```
- `callbacks`：执行回调方法，当并行任务执行完后，以非阻塞方式执行callbacks

_演示：_
```go
var count = 0
Parallel(func() { count += 1}).Add(func() { count += 2 }).ContinueWith(func() {
    count += 3
})
count = 10  // 由于异步，这里会优先执行
time.Sleep(10 * time.Millisecond)
// count = 16 // 最终结果
```
