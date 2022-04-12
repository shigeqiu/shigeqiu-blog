# Hystrix

配置 `application.yml` 用来开启
``` yml
feign:
  hystrix:
    enabled: true
```

## 机制

- 防止单个服务的故障耗尽整个服务的Servlet容器（例如：Tomcat）的线程资源。
- 快速失败机制，如果某个服务出现了故障，则调用该服务的请求快速失败，而不是线程等待。
- 提供回退（fallback）方案，在请求发生故障时，提供设计好的回退方案。
- 使用熔断机制，防止故障扩散到其他服务。
- 提供熔断器的监控组件（Hystrix Dashboard），可以实时监控熔断器的状态。


## 超时机制

> 官方说明 https://github.com/Netflix/Hystrix/wiki/Configuration#execution.isolation.thread.timeoutInMilliseconds

``` yml
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 60000  #默认是 1000
```
断路器的超时时间需要大于 Ribbon 的超时时间，不然不会触发重试。