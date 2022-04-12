# Spring Cloud Config

## 服务端配置

默认的端口号 `8888`。

## http 获取

HTTP服务资源的构成:
```
/{application}/{profile}[/{label}]
/{application}-{profile}.yml
/{label}/{application}-{profile}.yml
/{application}-{profile}.properties
/{label}/{application}-{profile}.properties
```

- application 是 SpringApplication 的 spring.config.name,(一般来说'application'是一个常规的Spring Boot应用),表示应用名称
- profile是一个active的profile(或者逗号分隔的属性列表)
- label是一个可选的git标签(默认为"master").
- profile:表示获取指定环境下配置，例如开发环境、测试环境、生产环境 默认值default，实际开发中可以是 dev、test、demo、production等


## 客户端配置
创建bootstrap.properties文件，其内容如下：
```
spring.application.name: foo
spring.cloud.config.env:default
spring.cloud.config.label:master
spring.cloud.config.uri:http://localhost:8888
```
其中 spring.application.name 为应用名称，spring.cloud.config.uri 配置config-server暴露的获取配置接口,其默认值为http://localhost:8888 
第二三项配置在前面已经提到过，配置的都为默认值，因此bootstrap.properties只需要配置应用名即可.

