
# Docker

<!-- TOC -->

- [Docker](#docker)
  - [参考文档](#%e5%8f%82%e8%80%83%e6%96%87%e6%a1%a3)
  - [镜像](#%e9%95%9c%e5%83%8f)
  - [容器](#%e5%ae%b9%e5%99%a8)
  - [仓库](#%e4%bb%93%e5%ba%93)
  - [centos7安装](#centos7%e5%ae%89%e8%a3%85)
  - [镜像下载](#%e9%95%9c%e5%83%8f%e4%b8%8b%e8%bd%bd)
  - [rm](#rm)
  - [ps](#ps)
  - [run](#run)
  - [进入容器](#%e8%bf%9b%e5%85%a5%e5%ae%b9%e5%99%a8)
  - [容器打包](#%e5%ae%b9%e5%99%a8%e6%89%93%e5%8c%85)
  - [Dockerfile](#dockerfile)
  - [build](#build)
  - [重要参数](#%e9%87%8d%e8%a6%81%e5%8f%82%e6%95%b0)

<!-- /TOC -->

## 参考文档

- 官方文档-build: https://docs.docker.com/engine/reference/builder/
- 容器与镜像: https://segmentfault.com/a/1190000002766882

## 镜像

Docker镜像就是一个只读的模板。  

例如：一个镜像可以包含一个完整的ubuntu操作系统环境，里面仅安装了Apache或用户需要的其它应用
程序。


镜像可以用来创建Docker容器。Docker提供了一个很简单的机制来创建镜像或者更新现有的镜像，用户甚至可以直接从其他人那里下载一
个已经做好的镜像来直接使用。

## 容器

Docker利用容器来运行应用。

容器是从镜像创建的运行实例。它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全
的平台。

可以把容器看做是一个简易版的Linux环境（包括root用户权限、进程空间、用户空间和网络空间等）和运
行在其中的应用程序。

> * 注：镜像是只读的，容器在启动的时候创建一层可写层作为最上层。

## 仓库

仓库是集中存放镜像文件的场所。有时候会把仓库和仓库注册服务器（Registry）混为一谈，并不严格区
分。实际上，仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像，每个镜像有不同的
标签（tag）。

仓库分为公开仓库（Public）和私有仓库（Private）两种形式。

> * 注：Docker	仓库的概念跟 Git 类似，注册服务器可以理解为	GitHub	这样的托管服务。

## centos7安装

安装
```
# yum install docker
```
启动服务
```
# systemctl start  docker.service
```
开机启动
```
# systemctl enable docker.service
```
是否安装成功
```
# docker version
```
## 镜像下载

下载镜像
```
# docker pull daocloud.io/centos:7
```
查看已有的镜像
```
# sudo docker images
```

## rm
删除容器(可一次性删除多个)
```
# docker rm name1 name2 name3 ...
```

## ps

查看正在运行的容器
```
# docker ps
```
查看所有容器
```
# docker ps -a
```

## run
以守护态运行容器
```
# docker run -d  centos:7 /bin/sh -c "while true; do echo hello world; sleep 1; done"
```
创建容器，并进入
> -i（交互式）和 -t（临时终端）

```
# docker run -t -i --name tomcat8 centos:7

# docker run -it --name tomcat8 centos:7
```

挂载本地目录,通过-v参数，冒号前为宿主机目录，必须为绝对路径，冒号后为镜像内挂载的路径。
```
docker run --privileged=true -v /root/software/:/mnt/software/ centos:7
```

端口映射
```
# docker run -p ip:hostPort:containerPort redis

    - iP表示主机的IP地址。 (-p 参数可添加多个)
    - hostPort表示宿主机的端口。 
    - containerPort表示虚拟机的端口。
```


## 进入容器
使用`docker attach`命令进入正在运行中的容器
> 只要这个连接终止，或者使用了exit命令，容器就会退出后台运行

```
# docker attach name/id号
```
 
 
使用`docker exec`命令进入正在运行中的容器
> 这个命令使用exit命令后，不会退出后台，**一般使用这个命令**，使用方法如下,/bin/sh 是固定写法

```
# docker exec -it name/id号 /bin/sh 
```

## 容器打包
将容器打包成镜像
```
# docker commit -a "Jordan Bach" -m "saved my message" insane_wright jbgo/message:v0.0.1

  -a, --author=""     Author (e.g., "John Hannibal Smith <hannibal@a-team.com>")
  -m, --message=""    Commit message
  -p, --pause=true    Pause container during commit
```


## Dockerfile

```
FROM daocloud.io/java:8u40-b22
COPY . /usr/stopper/conf
WORKDIR /usr/stopper/conf
ENTRYPOINT java -jar  bluelee-web-1.0.0-SNAPSHOT.jar
```
## build 

```
# docker build -t bluelee:1.0.0 .
```
- `-t` 自定义镜像的名称

```
# docker build [OPTIONS] PATH | URL |-Build a new image from the source code at PATH
```
PATH or URL  在这2项中的文件被当作资源上下文. 创建image过程中，所有的文件都可能会被标记,比如说执行 ADD (link is external) 项. 当一个Dockerfile只有 URL STDIN (docker build - < Dockerfile), 那么就没有上下文了.


如果 URL 中指定了一个git repo，那么这个git repo也会被使用。 这个git repo会被当作子目录 (git clone -recursive). A fresh git clone occurs in a temporary directory on your local host, and then this is sent to the Docker daemon as the context. 反正如果使用git你必须处理好git的凭证和假如需要的VPN设置.

 .dockerignore 在 PATH 项的根目录，这提供一种指定忽略的方式. 符合忽略规则的文件或目录将被忽略。


## 重要参数

|选项|说明|
|---|---|
| -i, --interactive |保持标准输入打开，默认为false|
| -t, --tty | 是否分配一个伪终端，默认为false|