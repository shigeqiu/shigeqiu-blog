# 包管理

<!-- TOC -->

- [包管理](#包管理)
  - [包管理](#包管理-1)
  - [Go Mod](#go-mod)
    - [方式一（推荐）:](#方式一推荐)
    - [方式二（使用go mod replace）](#方式二使用go-mod-replace)

<!-- /TOC -->


## 包管理

- Go Mod
- Go Vendor 
- Go dep
- Go Modules

## Go Mod

> Go Module下使用本地包 https://www.cnblogs.com/dexte/p/12337220.html


### 方式一（推荐）:

严格的说，方式一是使用项目目录下的go文件。
项目目录如下：

```
|── studyModule //项目主目录

| |──log //主目录下文件夹

| | |──log.go // log目录下go文件

| |── main.go // 主目录下go文件

| |── go.mod

```

log.go 中首行 package log，注意，log文件夹下不要go mod init，否则会导致无法编译

studyModule文件夹下 go mod init sts

main.go中调用log.go中的方法：import "sts/log"-> 此处为主目录下的mod名下边的log文件夹

log目录下的方法,变量等依然需要大写。如果不能正常使用可尝试在主目录下执行go mod tidy


### 方式二（使用go mod replace）
```
|── studyModule //项目主目录

| |──4fan.top //主目录下文件夹

| |──|──DY

| | | |──DYlogger@v1.1.0

| | | | | |──*.go

| | | | | |──go.mod【2】

| |── main.go // 主目录下go文件

| |── go.mod【1】

```

go.mod【2】：Module Name：DYlogger

go.mod 【1】：

require 4fan.top/DY/DYlogger v1.1.0

replace 4fan.top/DY/DYlogger => ./4fan.top/DY/DYlogger@v1.1.0

main.go中： import "4fan.top/DY/DYlogger"

说明：go mod是一个理想化的包管理工具，因此版本在go mod中很重要。(semver(语义化版本号))

go.mod 【1】中的require最好指明版本 对于文件名名字以及mod文件中的版本号码，未测试在其它情况下是否生效，请自行测试。