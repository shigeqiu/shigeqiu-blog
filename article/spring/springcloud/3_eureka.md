# Eureka

<!-- TOC -->

- [Eureka](#eureka)
    - [Server](#server)
    - [client](#client)
        - [服务续约](#服务续约)

<!-- /TOC -->




## Server

eurkeka 服务器配置

``` yml
eureka:
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

| 配置项  | 说明  |
|---|---|
| registerWithEureka=false  |  禁止向eureka注册服务，因为它自己本身就是服务器 |
|fetchRegistry=false | 这里不需要抓取注册表 |


> 自我保护模式正是一种针对网络异常波动的安全保护措施，使用自我保护模式能使Eureka集群更加的健壮、稳定的运行。

有三个地方会重新计算自我保护阀值numberOfRenewsPerMinThreshold。

1. 当新服务注册（register）时
1. 当服务退出（cancel）时
1. TimerTask定时任务（默认15分钟）

Eureka服务器自我保护模式开启的条件是：当Eureka服务器每分钟收到心跳续租的数量低于一个阈值，就会触发自我保护模式。当它收到的心跳数重新恢复到阈值以上时，该Eureka服务器节点才会自动退出自我保护模式。心跳阀值计算公式如下：
```
每个instance的预期心跳数目 = 60/每个instance的心跳间隔秒数

阈值 = 服务实例总数量×（60/每个instance的心跳间隔秒数）×自我保护系数（0.85）
```
其中，对于每个实例心跳间隔秒数前面已经讲过是30秒，可以通过 `eureka.instance. lease-renewal-interval-in-seconds` 属性进行修改。自我保护系数的默认值为 `0.85` ，该值可以通过 `eureka.server.renewal-percent-threshold` 属性进行更改。也就是说，在默认情况下，如果有15%的Eureka客户端的心跳包丢失时，Eureka服务器就会进入自我保护模式。

默认情况下Eureka服务器还会以每5分钟的间隔从与它对等的节点（这些节点由eureka.client.service-url.defaultZone配置指定）中复制所有的服务注册数据以达到同步的目的，如果这个同步因为某种原因导致失败，也会让Eureka服务器进入自我保护状态。示例中我们只构建了一个Eureka服务器，并没有其他对等的节点，所以无法同步其他服务注册信息，因此5分钟后会在Eureka的控制台上看到之前所示的告警信息。我们可以通过 `eureka.server.wait-time-in-ms-when-sync-empty` 属性配置来设置这个时间间隔，或者直接禁用自我保护模式。

默认情况下，Eureka Server 的自我保护模式是开启的，如果需要关闭，则在配置文件中添加如下代码：

``` yml
eureka:
  server:
    enable-self-preservation: false #关闭自我保护机制
```

清理无效节点的时间间隔，默认60秒
``` yml
eureka:
  server:
      evictionIntervalTimerInMs: 60000
```

## client

### 服务续约

当服务启动并成功注册到Eureka服务器后，Eureka客户端会默认以 **每隔30秒** 的频率向Eureka服务器发送一次心跳（可以在配置文件中通过`eureka.instance.lease-renewal-interval-in-seconds`属性进行更改）。
发送心跳起始就是执行服务续约（Renew）操作，避免自己的注册信息被Eureka服务器剔除。续约的处理逻辑和与服务注册逻辑基本一致：首先更新自身状态，然后同步到其他Eureka服务器节点。
对于Eureka服务器来说如果在默认的时间内（ **90秒** ），也就是连续3次没有收到客户端的心跳，则会将该服务实例从所维护的服务注册表中剔除，以禁止流向该实例的流量。不过，如果当Eureka服务器处于自我保护模式，则不会清除该服务实例信息。我们可以通过`eureka.instance.lease-expiration-duration-in-seconds`来指定这个时间。

> 注意，如果该值设置得太大，即使服务实例已经不存在，也可能会有流量路由到该服务实例，造成服务调用失败。而如果设置太小，很可能因为网络问题导致服务实例误被Eureka服务器从服务注册表中剔除。因此，Eureka官方建议我们最好不要修改这两个属性的配置。

``` yml
eureka:
  instance:
      lease-renewal-interval-in-seconds: 3
      lease-expiration-duration-in-seconds: 10

```

