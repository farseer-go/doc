# 静态资源
> webapi使得我们加载静态资源变的非常简单

在`webapi.Run()`之前调用`UseStaticFiles()`便能加载

```go
webapi.Area("/api/", func() {
    webapi.RegisterPOST("/projectgroup/tolist", projectGroupApp.ToPageList, "pageSize", "pageIndex")
})

webapi.UseStaticFiles() // 加载静态资源
webapi.UseCors()
webapi.UseApiResponse()
webapi.Run()
```

当调用了UseStaticFiles()，会在`./wwwroot/`目录中加载静态文件

_目录_
```
./wwwroot/
  - index.html
  - index.css
  - index.js
```
对应url：
- http://localhost:8888/index.html
- http://localhost:8888/index.css
- http://localhost:8888/index.js

!> 静态资源不需要一个个映射，只需要`UseStaticFiles()`开启后，便能自动映射`./wwwroot/`目录的静态资源