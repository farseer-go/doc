# exception异常处理
[English Document](https://farseer-go.gitee.io/en-us/)、[中文文档](https://farseer-go.gitee.io/)、[English Document](https://farseer-go.github.io/doc/en-us/)、[github Source](https://github.com/farseer-go/fs)
> 包：`"github.com/farseer-go/fs/exception"`

在go中`不建议使用panic来抛出异常`，建议使用`多参数返回值`来达到类似的效果。

!> 但有时候，使用类似.net core 、java这种异常机制来处理，反而会使得逻辑更加通顺
