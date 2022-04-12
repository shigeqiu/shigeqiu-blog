# 代码结构

- spring-boot-starters
   - spring-boot-starter
   - spring-boot-starter-activemq
   - spring-boot-starter-amqp
   - spring-boot-starter-aop
   - spring-boot-starter-artemis
   - spring-boot-starter-batch
   - spring-boot-starter-cache
   - spring-boot-starter-cloud-connectors
   - spring-boot-starter-data-cassandra
   - spring-boot-starter-data-cassandra-reactive
   - spring-boot-starter-data-couchbase
   - spring-boot-starter-data-elasticsearch
   - spring-boot-starter-data-jpa
   - spring-boot-starter-data-ldap
   - spring-boot-starter-data-mongodb
   - spring-boot-starter-data-mongodb-reactive
   - spring-boot-starter-data-neo4j
   - spring-boot-starter-data-redis
   - spring-boot-starter-data-redis-reactive
   - spring-boot-starter-data-rest
   - spring-boot-starter-data-solr
   - spring-boot-starter-freemarker
   - spring-boot-starter-groovy-templates
   - spring-boot-starter-hateoas
   - spring-boot-starter-integration
   - spring-boot-starter-jdbc
   - spring-boot-starter-jersey
   - spring-boot-starter-jetty
   - spring-boot-starter-jooq
   - spring-boot-starter-json
   - spring-boot-starter-jta-atomikos
   - spring-boot-starter-jta-bitronix
   - spring-boot-starter-jta-narayana
   - spring-boot-starter-logging
   - spring-boot-starter-log4j2
   - spring-boot-starter-mail
   - spring-boot-starter-mobile
   - spring-boot-starter-mustache
   - spring-boot-starter-actuator
   - spring-boot-starter-parent
   - spring-boot-starter-quartz
   - spring-boot-starter-reactor-netty
   - spring-boot-starter-security
   - spring-boot-starter-social-facebook
   - spring-boot-starter-social-twitter
   - spring-boot-starter-social-linkedin
   - spring-boot-starter-test
   - spring-boot-starter-thymeleaf
   - spring-boot-starter-tomcat
   - spring-boot-starter-undertow
   - spring-boot-starter-validation
   - spring-boot-starter-web
   - spring-boot-starter-webflux
   - spring-boot-starter-websocket
   - spring-boot-starter-web-services




# Spring Boot 项目结构maven关系分析

**modules聚合**
- spring-boot-parent的pom里边，存在`<modules />`标签
- spring-boot-build的pom里边，存在`<modules />`标签

**dependency**
- spring-boot-dependencies (pom)，定义了dependencyManagement
- spring-boot-parent (pom)，定义了dependencyManagement
