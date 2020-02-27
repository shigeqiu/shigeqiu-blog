# redis 安装

<!-- TOC -->

- [redis 安装](#redis-%E5%AE%89%E8%A3%85)
  - [配置](#%E9%85%8D%E7%BD%AE)
  - [启动停止服务](#%E5%90%AF%E5%8A%A8%E5%81%9C%E6%AD%A2%E6%9C%8D%E5%8A%A1)

<!-- /TOC -->

## 配置

绑定的主机地址，注释掉以后你的机器就可以远程访问了
```
bind 127.0.0.1
```
后台启动，改成 yes，意思是是否要后台启动，（默认为no）
```
daemonize no
```
当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以通过pidfile指定
```
pidfile /var/run/redis.pid
```

设置密码选项
```
requirepass youpassword 
```

设置日志，在冒号里面输入日志的路径，比如
```
logfile “/usr/local/redis/log/redis.log”
```
其中log是新建的文件夹，redis.log也是新建的文件（有人说只要新建到文件夹就可以了，redis.log会自动生成，不过我没有测试）。

备份文件路径：
```
# The filename where to dump the DB
# 备份文件的文件名
dbfilename dump.rdb

# The working directory.
#
# The DB will be written inside this directory, with the filename specified
# above using the 'dbfilename' configuration directive.
#
# The Append Only File will also be created inside this directory.
#
# Note that you must specify a directory here, not a file name.
# 数据库备份的文件放置的路径
# 路径跟文件名分开配置是因为 Redis 备份时，先会将当前数据库的状态写入到一个临时文件
# 等备份完成时，再把该临时文件替换为上面所指定的文件
# 而临时文件和上面所配置的备份文件都会放在这个指定的路径当中
# 默认值为 ./
dir /data/data/redis/
```


## 启动停止服务

启动服务

通过指定配置文件启动，可以为redis服务启动指定配置文件，输入命令：
```
$ ./redis-server /etc/redis/6379.conf
```
停止Redis服务

- 通过redis-cli连接服务器后执行shutdown命令，则执行停止redis服务操作。
- shutdown还有一个参数，代表关闭redis服务前是否生产持久化文件
```
shutdown  save|nosave
```