# Spring Boot 基础



<!-- TOC depthFrom:1 depthTo:6 orderedList:false -->

- [SpringBoot概念](#springboot概念)
    - [概述](#概述)
    - [Spring Initializr](#spring-initializr)
    - [起步依赖](#起步依赖)
    - [自动配置](#自动配置)
        - [覆盖自动配置](#覆盖自动配置)
        - [自动配置微调](#自动配置微调)
    - [网址参考](#网址参考)

<!-- /TOC -->


## 概述

- **自动配置**：针对很多Spring应用程序常见的应用功能，Spring Boot能自动提供相关配置。
- **起步依赖**：告诉Spring Boot需要什么功能，它就能引入需要的库。
- **命令行界面**：这是Spring Boot的可选特性，借此你只需写代码就能完成完整的应用程序，
无需传统项目构建。
- **Actuator**：让你能够深入运行中的Spring Boot应用程序，一探究竟。

> Spring Boot的设计是加载应用级配置，随后再考虑自动配置类。


``` java
@Bean
@ConditionalOnMissingBean(JdbcOperations.class)
public JdbcTemplate jdbcTemplate() {
 return new JdbcTemplate(this.dataSource);
}
```
- **@ConditionalOnMissingBean** 注解，要求当前不存在JdbcOperations
类型（JdbcTemplate实现了该接口）的Bean时才生效。

## Spring Initializr

Spring Initializr从本质上来说就是一个Web应用程序
，它能为你生成Spring Boot项目结构。虽
然不能生成应用程序代码，但它能为你提供一个基本的项
目结构，以及一个用于构建代码的
Maven或Gradle构建说明文件。你只需要写应用程序的代
码就好了。


Spring Initializr有几种用法。

- 通过Web界面使用。
- 通过Spring Tool Suite使用。
- 通过IntelliJ IDEA使用。
- 使用Spring Boot CLI使用。


## 起步依赖

与普通的继承一样，这个简单的例子继承自parent标签里的所有依赖。与平时我们自己的parent里边儿管理的JAR包的版本类似。

```xml
<parent>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-parent</artifactId>
	<version>1.5.2.RELEASE</version>
	<relativePath/> <!-- lookup parent from repository -->
</parent>
```

 从技术角度来看，我们要
用Spring MVC来处理Web请求，用Thymeleaf来定义Web视图，用Spring Data
JPA来把阅读列表持久化到数据库里，姑且先用嵌入式的H2数据库。虽然也可以用Groovy，但是我们还是先用Java来开发这个
应用程序吧。此外，我们使用Gradle作为构建工具。无论是用Web界面、Spring Tool Suite还是IntelliJ
IDEA，只要用了Initializr，你就要确保勾选了Web、Thymeleaf和JPA这几个复选框。还要记得勾上H2复选框，这样才能在应
用程序时使用这个内嵌式数据库。


## 自动配置

在向应用程序加入Spring Boot时，有个名为 **spring-boot-autoconfigure**的JAR文件，其中包含了很多配置类。每个配置类都在应用程序的Classpath里，都有机会为应用程序的配置添砖加瓦。这些配置类里有用于Thymeleaf的配置，有用于Spring Data JPA的配置，有用于Spiring MVC的配置，
还有很多其他东西的配置，你可以自己选择是否在Spring应用程序里使用它们。



### 覆盖自动配置


```java
@Bean
@ConditionalOnMissingBean(JdbcOperations.class)
public JdbcTemplate jdbcTemplate() {
 return new JdbcTemplate(this.dataSource);
}
```
- **@ConditionalOnMissingBean** 注解，要求当前不存在JdbcOperations
类型（JdbcTemplate实现了该接口）的Bean时才生效。

关于Spring Security 自动配置

```java
@Configuration
@EnableConfigurationProperties
@ConditionalOnClass({ EnableWebSecurity.class })
@ConditionalOnMissingBean(WebSecurityConfiguration.class)
@ConditionalOnWebApplication
public class SpringBootWebSecurityConfiguration {
...
}

```
- **@ConditionalOnClass** Classpath里必须要有 ***@EnableWebSecurity*** 注解
- **@ConditionalOnWebApplication** 这必须是个 Web 应用程序
- **@ConditionalOnMissingBean** 要求当下没有WebSecurityConfiguration类型的
Bean。


自定义覆盖 Spring Security

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
···
}
```
- **@EnableWebSecurity** 我们实际上间接创建了一个WebSecurityConfiguration Bean。所以在自动
配置时，这个Bean就已经存在了，@ConditionalOnMissingBean条件不成立，SpringBootWebSecurityConfiguration提供的配置就被跳过了。

虽然Spring Boot的自动配置和@ConditionalOnMissingBean让你能显式地覆盖那些可以
自动配置的Bean，但并不是每次都要做到这种程度。

### 自动配置微调
有300多个属性可以用来微调Spring Boot应用程序里的Bean

一些例子：
-  禁用模板缓存
-  配置嵌入式服务器
-  配置日志
-  配置数据源


## 网址参考

-  [【官网】Spring Initializr > Web界面使用](http://start.spring.io)

- [【官网】spring boot 官方文档](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-documentation)

- [【官网】附录A - 常用属性](http://docs.spring.io/spring-boot/docs/1.2.3.RELEASE/reference/html/common-application-properties.html)

- [【官方】Spring + MyBatis 配置](http://www.mybatis.org/spring-boot-starter/mybatis-spring-boot-test-autoconfigure/project-info.html)
