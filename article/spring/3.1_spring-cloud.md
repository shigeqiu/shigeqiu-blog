# Spring Cloud 基础

<!-- TOC -->

- [Spring Cloud 基础](#spring-cloud-基础)
    - [注解](#注解)
    - [依赖](#依赖)
        - [feign](#feign)
        - [netflix](#netflix)
        - [gateway](#gateway)
    - [参考资料](#参考资料)

<!-- /TOC -->

## 注解

- `@SpringCloudApplication`
- `@EnableDiscoveryClient` 不仅可以开启eureka客户端，还有consul、zookeeper
- `@EnableEurekaClient` 开启eureka客户端可以调用在eureka注册的服务
- `@EnableFeignClients` 开启Feign
- `@EnableCircuitBreaker` 开启Hystrix熔断机制


## 依赖

### feign

``` xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-openfeign</artifactId>
</dependency>
```

### netflix

``` xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-ribbon</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-hystrix-dashboard</artifactId>
</dependency>
```




### gateway

``` xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-gateway</artifactId>
</dependency>
```

## 参考资料

- Spring Cloud中文网-官方文档中文版 https://springcloud.cc/
- Spring Cloud中国社区 http://springcloud.cn/
- Spring Cloud http://projects.spring.io/spring-cloud/