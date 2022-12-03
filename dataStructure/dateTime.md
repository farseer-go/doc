# dateTime 时间日期类型
> 包：`"github.com/farseer-go/fs/dateTime"`

这是对go原生的time.Time类型做了进一步的封装

## 初始化
```go
// 根据time.Time初始化
dt := dateTime.New(time.Date(2022, 9, 06, 21, 14, 25, 0, time.Local))
```

## 1、Now 获取当前时间
```go
dt := dateTime.Now()
```

## 2、ToString 格式化时间
```go
dateTime.Now().ToString("yyyy-MM-dd HH:mm:ss")
```
_执行结果：_

?> "2022-09-06 21:14:25"

## 3、Year 获取年 int
```go
dateTime.Now().Year()
```
_执行结果：_

?> 2022

## 4、Month 获取月 int
```go
dateTime.Now().Month()
```
_执行结果：_

?> 9

## 5、Day 获取天 int
```go
dateTime.Now().Day()
```
_执行结果：_

?> 6

## 6、Hour 获取小时 int
```go
dateTime.Now().Hour()
```
_执行结果：_

?> 21

## 7、Minute 获取分钟 int
```go
dateTime.Now().Minute()
```
_执行结果：_

?> 14

## 8、Second 获取秒 int
```go
dateTime.Now().Second()
```
_执行结果：_

?> 25

## 9、Date 获取日期
```go
dateTime.Now().Date
```

?> 返回的是DateTime类型，只有日期部份，Time部份为00:00:00

## 10、AddDate 添加日期部份
```go
dateTime.Now().AddDate(1, 2, 3).ToString("yyyy-MM-dd HH:mm:ss")
```
_执行结果：_

?> "2023-11-09 21:14:25"

## 11、AddTime 添加Time部份
```go
dateTime.Now().AddTime(1, 2, 3).ToString("yyyy-MM-dd HH:mm:ss")
```
_执行结果：_

?> "2022-09-06 22:16:28"

## 12、AddYears 添加年
```go
dateTime.Now().AddYears(2).ToString("yyyy-MM-dd HH:mm:ss")
```
_执行结果：_

?> "2024-09-06 21:14:25"

## 13、AddMonths 添加月份
```go
dateTime.Now().AddMonths(-1).ToString("yyyy-MM-dd HH:mm:ss")
```
_执行结果：_

?> "2022-08-06 21:14:25"

## 14、AddDays 添加天数
```go
dateTime.Now().AddDays(2).ToString("yyyy-MM-dd HH:mm:ss")
```
_执行结果：_

?> "2022-09-08 21:14:25"

## 15、AddHours 添加小时
```go
dateTime.Now().AddHours(-1).ToString("yyyy-MM-dd HH:mm:ss")
```
_执行结果：_

?> "2022-09-06 20:14:25"

## 16、AddMinutes 添加分钟
```go
dateTime.Now().AddMinutes(2).ToString("yyyy-MM-dd HH:mm:ss")
```
_执行结果：_

?> "2022-09-06 21:16:25"

## 17、AddSeconds 添加秒
```go
dateTime.Now().AddSeconds(2).ToString("yyyy-MM-dd HH:mm:ss")
```
_执行结果：_

?> "2022-09-06 21:14:27"

## 18、ToTime  转换成time.Time
```go
dateTime.Now().ToTime()
```

?> 转换成time.Time类型