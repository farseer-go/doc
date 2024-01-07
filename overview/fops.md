# 上报FOPS

如果你安装了FOPS服务，希望你的应用可以将`日志`、`健康检查`、`链路记录`上报到FOPS，只需要通过./farseer.yaml配置服务地址即可。

```yaml
Fops:
  Server: "https://fops.fsgit.com"
LinkTrace:
  Enable: true
  PrintLog: true
```

当框架启动后，如果检查到`Fops.Server`有配置地址。则定时将`日志`、`健康检查结果`上报到FOPS中心。

同时如果`LinkTrace.Enable = true`时，将开启链路追踪，并将记录上报到FOPS中心。

除此之外，你不需要做任何的代码设置。

