# Filter配置


<!-- TOC -->

- [Filter配置](#filter配置)
    - [纯Web配置](#纯web配置)
        - [web.xml配置](#webxml配置)
        - [ini配置](#ini配置)
        - [EnvironmentLoader](#environmentloader)
    - [spring集成配置主要的类](#spring集成配置主要的类)

<!-- /TOC -->

## 纯Web配置 

### web.xml配置

**Shiro 1.2及以后版本的配置方式**   
从Shiro 1.2开始引入了Environment/WebEnvironment的概念，即由它们的实现提供相应的SecurityManager及其相应的依赖。ShiroFilter会自动找到Environment然后获取相应的依赖。

```
<listener>  
   <listener-class>org.apache.shiro.web.env.EnvironmentLoaderListener</listener-class>  
</listener>   

<context-param>  
   <param-name>shiroEnvironmentClass</param-name>  
   <param-value>org.apache.shiro.web.env.IniWebEnvironment</param-value>  
</context-param>  
<context-param>  
    <param-name>shiroConfigLocations</param-name>  
    <param-value>classpath:shiro.ini</param-value>  
</context-param>   
```

### ini配置
ini配置部分和之前的相比将多出对url部分的配置。     
```
[main]  
#默认是/login.jsp  
authc.loginUrl=/login  
roles.unauthorizedUrl=/unauthorized  
perms.unauthorizedUrl=/unauthorized  
[users]  
zhang=123,admin  
wang=123  
[roles]  
admin=user:*,menu:*  
[urls]  
/login=anon  
/unauthorized=anon  
/static/**=anon  
/authenticated=authc  
/role=authc,roles[admin]  
/permission=authc,perms["user:create"]   
```

### EnvironmentLoader

1. **初始化，主要走两个方法**
    - 第一个，初始化走`initEnvironment(ServletContext servletContext)`这个方法。该方法会将`WebEnvironment`对象插入到ServletContext中
        ```java
        WebEnvironment environment = createEnvironment(servletContext);
        servletContext.setAttribute(ENVIRONMENT_ATTRIBUTE_KEY, environment);
        /**
        * key = org.apache.shiro.web.env.EnvironmentLoader.ENVIRONMENT_ATTRIBUTE_KEY
        */
        ```
    - 第二个，`createEnvironment(servletContext);`方法会检测配置是否为空，并将配置进行set方法，会将`WebEnvironment`对象进行初始化操作。也就是该对象的`init()`方法。
        ```java
        MutableWebEnvironment environment = (MutableWebEnvironment) ClassUtils.newInstance(clazz);
        environment.setServletContext(sc);
        ((ResourceConfigurable) environment).setConfigLocations(configLocations);
        
        LifecycleUtils.init(environment);
        ```

    - 最后效果 : `WebEnvironment ` 对象  ---插入->  `ServletContext`对象


## spring集成配置主要的类

- `SpringShiroFilter`与`ShiroFilterFactoryBean`