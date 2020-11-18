# 分布式 Distributed

## 理论

- [CAP理论](CAP.md)
- [架构的基本概念](架构的基本概念.md)

## 文章

- [Leaf-来自美团点评的分布式ID生成系统](Leaf-来自美团点评的分布式ID生成系统.md)
- [Leaf-美团分布式ID生成服务开源](Leaf-美团分布式ID生成服务开源.md)
- [虎牙直播在微服务改造方面的实践和总结](虎牙直播在微服务改造方面的实践和总结.md)
- [淘宝千万级并发分布式架构的14次演进](淘宝千万级并发分布式架构的14次演进.md)
- [RPC 框架介绍](RPC框架介绍.md)

## 分布式锁

- [Redis和Zookeeper实现](Redis和Zookeeper实现.md)
- [Redis分布式锁之Redlock实现](Redis分布式锁之Redlock实现.md)
- [Redis的争议](Redis的争议.md)

## 定时任务的分布式调度

- [Cron表达式](Cron表达式.md)
- Spring Task
- quartz的集群解决方案
- TBSchedule
- 当当开源 Elastic-job https://github.com/elasticjob
- 唯品会开源Saturn https://github.com/vipshop/Saturn

## 分布式事务

- [分布式事务是什么](分布式事务是什么.md)
- [分布式事务](分布式事务.md)


## 序列化

- Apache Avro
- Apache Thrift
- Google Protocol Buffers

## 中间件

- MyCat
- Sharding JDBC

## 大数据

- Hadoop的MapReduce
- Storm
- Spark
- Flink

## 数据库

- PostgreSQL

## 算法

   在做服务器负载均衡时候可供选择的负载均衡的算法有很多，包括： 轮循算法(Round Robin)、哈希算法(HASH)、最少连接算法(Least Connection)、响应速度算法(Response Time)、加权法(Weighted )等。其中哈希算法是最为常用的算法.

  典型的应用场景是： 有N台服务器提供缓存服务，需要对服务器进行负载均衡，将请求平均分发到每台服务器上，每台机器负责1/N的服务。

  常用的算法是对hash结果取余数 (hash() mod N )：对机器编号从0到N-1，按照自定义的 hash()算法，对每个请求的hash()值按N取模，得到余数i，然后将请求分发到编号为i的机器。但这样的算法方法存在致命问题，如果某一台机器宕机，那么应该落在该机器的请求就无法得到正确的处理，这时需要将当掉的服务器从算法从去除，此时候会有(N-1)/N的服务器的缓存数据需要重新进行计算;如果新增一台机器，会有N /(N+1)的服务器的缓存数据需要进行重新计算。对于系统而言，这通常是不可接受的颠簸(因为这意味着大量缓存的失效或者数据需要转移)。那么，如何设计一个负载均衡策略，使得受到影响的请求尽可能的少呢?

  在Memcached、Key-Value Store 、Bittorrent DHT、LVS中都采用了Consistent Hashing算法，可以说Consistent Hashing 是分布式系统负载均衡的首选算法。