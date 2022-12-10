# 使用哪种模式
在webapi组件中，提供了两种风格：`minimalApi`、`mvc`。

在决定使用哪种风格时，其实很容易选择，关键在于：
- [ ] 你的Api需要使用`Header`吗？
- [ ] 你的Api需要使用`Session`吗？
- [ ] 你的Api需要使用`Cookies`吗？
- [ ] 你的Api需要使用`httpContext`吗？
- [ ] 你的Api需要做`初始化`、执行`通用逻辑`吗？

如果以上的回答都是：`不需要`，则直接使用：`minimalApi`风格。

## `minimalApi`风格
`minimalApi`风格，非常的简单，不需要你依赖任何的webapi组件。对你的应用系统没有侵入性。
通常在我们的设计原则里面，依赖的越少越好。

另外`minimalApi`风格，我们也可以称为：`动态API`的方案。意味着，我们不需要在应用系统中专门分一个UI层来放API入口。而是直接通过路由绑定到业务层（或DDD中的应用层）

## `mvc`风格
而`mvc`风格，由于我们需要依赖`controller.BaseController`，通常说明我们需要这个`httpContext`来获取来自http的请求信息。

因此这种场景，毫无疑问只能使用`mvc`风格。

`mvc`除了支持httpContext外，还支持`构造函数`的风格，意味着在每个Action执行前，支持做通用的逻辑处理。