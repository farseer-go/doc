# 使用哪种模式
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/webapi)

在webapi组件中，提供了两种风格：`minimalApi`、`mvc`。

## 1、`minimalApi`风格
`minimalApi`风格，非常的简单，不需要你依赖任何的webapi组件。对你的应用系统没有侵入性。
通常在我们的设计原则里面，依赖的越少越好。

另外`minimalApi`风格，我们也可以称为：`动态API`的方案。意味着，我们不需要在应用系统中专门分一个UI层来放API入口。而是直接通过路由绑定到业务层（或DDD中的应用层）

## 2、`mvc`风格
`mvc`风格，需要依赖`controller.BaseController`，但可以自动注册所有该控制器下的API方法。

`mvc`还支持`ActionFilter过滤器`，意味着在每个Action执行前后，支持做通用的逻辑处理。