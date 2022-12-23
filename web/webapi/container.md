# 容器注入

webapi支持对已经注册过的接口自动注入，省去我们手动调用`container.Resolve`，此种注入在实际开发中会更方便。

```go
    fs.Initialize[StartupModule]("demo")
    webapi.Area("/api/1.0/", func () {
        webapi.RegisterPOST("/product/ToEntity", productApp.ToEntity)
    })
    webapi.UseApiResponse()
    webapi.Run()
```

从上方的代码，我们注册了一个`productApp.ToEntity` 动态API。

```go
package productApp

import (
	"fullExample/domain/products/product"
	"github.com/farseer-go/collections"
	"github.com/farseer-go/mapper"
)

// ToEntity 查看产品详细信息
// repository通过container自动注入实现进来
func ToEntity(productId int, repository product.Repository) DTO {
	do := repository.ToEntity(productId)
	dto := mapper.Single[DTO](do)
	return dto
}
```

ToEntity接收两个入参，int、product.Repository（接口）。当前端请求这个接口时，repository参数，会被自动注入：

![img.png](images/img_4.png)

![img.png](images/img_5.png)

此时，`repository`参数不需要我们手动去实例化或到container中获取。

接下来我们看下`product.Repository`的实现：
![img.png](images/img_6.png)

DB字段，也是通过自动注入，拿到实现的。

此处的代码示例，可以到[这里查看](https://github.com/farseer-go/demo/blob/main/fullExample/infrastructure/repository/productRepository.go)