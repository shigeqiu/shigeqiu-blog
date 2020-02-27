# Jenkins

## 插件介绍

- maven插件：`maven integration plugins`
- 远程部署插件：`Publish Over SSH`
- git 插件：`Git plugin`

## 注意事项


在jenkins中配置git路径一定要配到`bin`目录下的具体`git`命令，如:

正确的路径：`/usr/local/git/bin/git`，错误的路径：`/usr/local/git/bin`
