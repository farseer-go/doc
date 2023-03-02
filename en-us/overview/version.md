# 更新记录

## `fs` V**0.4.1** _（2022.12.20）_

1. 修复types.IsGoBasicType方法的BUG
2. 使用自研的配置组件（移除第三方组件）
3. 使用自研的container组件（移除第三方组件）
4. 增加parse.Convert中bool转string

## `webapi` V**0.2.0** _（2022.12.12）_

1. 修改：重命名handleMiddleware
2. 修改：mvc控制器，路由名称转为小写
3. 修改：mvc控制器，增加对Action的参数设置
4. 修复：Response输出报文时，非Go基础类型，均转成json
5. 修复：controller.BaseController初始化时的错误
6. 修复：异常内容转成string的错误
7. 新增：header自动绑定
8. 新增：IActionFilter过滤器
9. 新增：批量注册路由方法webapi.RegisterRoutes

## `collections` V**0.2.0** _（2022.12.12）_
1. 新增支持数据库varchar类型，按,号分割转换成list
2. 修复json序列化问题

## `fs` V**0.3.0** _（2022.12.12）_
1. 增加types.IsGoBasicType 是否为Go内置基础类型
2. 添加reflect.Type的类型转换（parse.ConvertValue）
3. 支持parse.Convert支持string转string[]


##  `farseer-go` V**0.1.0** _（2022.09.20）_ 
我们非常高兴地发布第一个外部版本号v0.1.0
 
在此之前，我们的团队一直在努力为框架添加各种功能，以满足业务系统的需求。 
3个月后，我们将常用的功能添加到框架中，并通过初步测试验证。 
 
因此，我们的团队决定发布这个版本号。 
需要注意的是，在v1到来之前，v0，仍然会有很多小问题。但是，我们会努力迅速修复发现的问题。 
如果你在使用过程中发现任何bug，也请发问题或PR，谢谢。