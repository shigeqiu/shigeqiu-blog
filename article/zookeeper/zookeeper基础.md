# ZooKeeper基础

<!-- TOC -->

- [ZooKeeper基础](#zookeeper基础)
  - [官方文档](#官方文档)
  - [安装](#安装)
  - [配置](#配置)
  - [zkServer](#zkserver)
  - [图形化客户端工具](#图形化客户端工具)
  - [znode](#znode)
    - [持久节点 persistent Nodes](#持久节点-persistent-nodes)
    - [短暂节点 Ephemeral Nodes](#短暂节点-ephemeral-nodes)
    - [顺序号 Sequence Nodes -- Unique Naming](#顺序号-sequence-nodes----unique-naming)
    - [观察 Watches](#观察-watches)
  - [应用](#应用)

<!-- /TOC -->

## 官方文档

> 官网 http://zookeeper.apache.org/

第一种方式，打开官网 > Getting Started , 有两个选项：

1. Learn about ... 阅读文档
1. Download  ... 下载

第二种方式，打开官网 -> 选择右侧边栏 -> Documentation ,选择对应的版本阅读文档


## 安装

zookeeper一共分为两种模式：
- **独立模式(standalone mode)**，即只有一个服务器
- **复制模式(replicate mode)** ，运行于计算机集群上

下载一个稳定的版本，然后找一个合适的位置解压,本次安装以 **独立模式** 安装：

```
$ tar zxf zookeeper-x.y.z.tar.gz
```

## 配置 

默认配置文件路径为 conf 目录下，文件名为`zoo.cfg`。进入`conf/`目录下可以看到一个`zoo_sample.cfg`文件，可供参考。

| 配置项  | 说明  |
|---|---|
| tickTime  | 基本时间（毫秒）  |
| dataDir  | 存储持久数据的本地文件系统位置  |
|  clientPort |  端口，默认2181 |


## zkServer

``` bash
[stopper@localhost bin]$ ./zkServer.sh 
Usage: ./zkServer.sh {start|start-foreground|stop|restart|status|upgrade|print-cmd}
```

| 选项 | 描述 |
|---|---|
| start  |  启动 |
| start-foreground  | 非后台进程启动运行 |
| stop  |  停止 |
|  restart | 重启  |
| status  |  状态 |
| upgrade  |  升级 |
|  print-cmd |  输出jar文件的启动命令 |


## 图形化客户端工具

- ZooInspector https://issues.apache.org/jira/secure/attachment/12436620/ZooInspector.zip
- zkui 
   - github https://github.com/DeemOpen/zkui 
   - gitee码云 https://gitee.com/ilanni/zkui
- idea插件： 搜索zookeeper安装

## znode

### 持久节点 persistent Nodes

ZooKeeper维护着一个树形层次结构，树中的节点被称为znode。znode可以用于存储数据。并且有一个与之相关联的ACL。一个znode存储的数据被限制在1MB以内。持久znode不依赖于客户端会话，只有当客户端（不一定是创建它的那个客户端）明确要删除该持久znode时才会被删除。

### 短暂节点 Ephemeral Nodes

创建短暂znode的客户端会话结束时，ZooKeeper会将该znode删除。短暂znode不可以有子节点，即使是短暂子节点。


### 顺序号 Sequence Nodes -- Unique Naming

顺序znode是指名称中包含ZooKeeper指定顺序号的znode。如果在创建znode时设置了顺序标识，那么该znode名称之后便会附加一个值，这个值是一个单调递增的计数器所添加的。该值对于父znode是唯一的。

### 观察 Watches

znode以某种方式发生变化时。“观察”（watch）机制可以让客户端得到通知。

## 应用

- 锁
- 队列

有时间再写。。。