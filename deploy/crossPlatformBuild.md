# 跨平台编译

## mac
```shell
RUN GOOS=darwin GOARCH=amd64 CGO_ENABLED=0 go build
```

## linux
```shell
RUN GOOS=linux GOARCH=amd64 CGO_ENABLED=0 go build
```

## windows
```shell
RUN GOOS=windows GOARCH=amd64 CGO_ENABLED=0 go build
```