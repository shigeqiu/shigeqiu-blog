# Golang

- [基础语法](base.md)
- [包管理](gomod.md)
- [web](web.md)
- [后台启动](后台启动)

## go test 

当go test以包列表模式运行时，go test会缓存成功的包的测试结果以避免不必要的重复测试。当然，有时候我们测试的时候并不喜欢有缓存，我们可以手动禁用缓存。可以通过下列方式禁用缓存：

带上 `-count=1` 参数禁用缓存。

如，执行下面命令测试，便会禁用缓存测试结果

```
go test -v -count=1 filename_test.go
```

手动清除测试缓存
除了在执行测试命令的时候加上禁用缓存参数，我们还可以执行下面的命令手动清除缓存，需要注 意的是，每次都得清除，不然下次执行的还是上次的结果。

```
go clean -testcache
```

VsCode `setting.xml` 配置

``` json
{
    "go.testFlags": ["-v","-count=1"],
}
```


## 工具

- github.com/spf13/viper

## 环境

windows 下打包 linux 的运行程序。



设置变量
```
set GOARCH=amd64
set GOOS=linux

go build
```
取消变量

```
set GOARCH=
set GOOS=
```
显示变量

```
set GO
```