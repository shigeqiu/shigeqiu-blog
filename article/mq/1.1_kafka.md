# Kafka

<!-- TOC -->

- [Kafka](#kafka)
    - [参考网址](#参考网址)
    - [安装](#安装)
    - [server.properties](#serverproperties)
    - [主题](#主题)

<!-- /TOC -->

## 参考网址

官网 http://kafka.apache.org/

## 安装

下载
```
[stopper@localhost download]$ sudo wget http://apache.cs.utah.edu/kafka/2.0.0/kafka_2.12-2.0.0.tgz
```

解压

```
[stopper@localhost data]$ sudo tar zxf download/kafka_2.12-2.0.0.tgz -C server/
```

启动

```
[stopper@localhost kafka_2.12-2.0.0]$ ./bin/kafka-server-start.sh 
USAGE: ./bin/kafka-server-start.sh [-daemon] server.properties [--override property=value]*

[stopper@localhost kafka_2.12-2.0.0]$ ./bin/kafka-server-start.sh -daemon ./config/server.properties 
```
启动之后默认端口为 9092

## server.properties

`server.properties`文件配置

`broker.id`

broker的id。必须为每个broker设置一个唯一的整数。

## 主题

创建主题

```
[stopper]$ ./kafka-topics.sh --list --zookeeper localhost:2181
[stopper]$ ./kafka-topics.sh --create --replication-factor 1 --partitions 1 --topic test --zookeeper localhost:2181
Created topic "test".

[stopper]$ ./kafka-topics.sh --list --zookeeper localhost:2181
test

```

往测试主题上发布消息

``` 

[stopper]$ ./kafka-console-producer.sh --broker-list localhost:9092 --topic test
>Test Message 1
>Test Message 2
>^C
```

从测试主题上读取信息

```

[stopper@localhost bin]$ ./kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning


Test Message 1
Test Message 2

```