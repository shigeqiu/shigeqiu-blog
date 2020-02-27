
# FilterChainResolver

<!-- TOC -->

- [FilterChainResolver](#filterchainresolver)
- [FilterChainResolver --- 相关联的类介绍](#filterchainresolver-----相关联的类介绍)
    - [ProxiedFilterChain](#proxiedfilterchain)
    - [SimpleNamedFilterList](#simplenamedfilterlist)
    - [DefaultFilterChainManager](#defaultfilterchainmanager)
    - [PathMatchingFilterChainResolver](#pathmatchingfilterchainresolver)
- [FilterChainResolver --- 加载的全过程](#filterchainresolver-----加载的全过程)
    - [FilterChainManager的创建过程](#filterchainmanager的创建过程)

<!-- /TOC -->

**概述**
- 自定义的filter加载,初始化
- 根据路径找寻匹配的自定义filter
- 重写了FilterChain

---


# FilterChainResolver --- 相关联的类介绍
## ProxiedFilterChain

`ProxiedFilterChain`继承自FilterChain,它重写了doFilter方法，并且该对象中包含有三个对象，分别是org,filters,index三个对象，
- orig是原有的FilterChain,也就是从web.xml进来时通过Filter标签传进来的。
- filters是NamedFilterList对象，同时该对象也继承自List,可以说该对象就是一个Filter链
- index是filters中的索引值，目的是将过滤链通过index++ 的方式循环过滤。

```java
public class ProxiedFilterChain implements FilterChain {
    private FilterChain orig;
    private List<Filter> filters;
    private int index = 0;
}
```


## SimpleNamedFilterList
`SimpleNamedFilterList`类继承自`NamedFilterList`,其中包含一个name值，和一个`List<Filter>`对象，也就是filter链。其发现于`DefaultFilterChainManager`类中。

## DefaultFilterChainManager
`DefaultFilterChainManager`类是一个默认的FilterChain管理类。其中包含三个属性，fiterConfig,filters,filterChains。该对象发现于`PathMatchingFilterChainResolver`对象中。
- filterConfig对象是通过web.xml中的filter传进来的，所以这个不做叙述。
- fiters代码备注上写的是‘用于创建链的过滤器池’，备注写的也挺合理的，它是个map结构，key为name，value为Filter类型。通过debug截取到如下信息，可见filters不仅放的是shiro默认的filter过滤器，然而其中还包含有自己创建的filter，如‘myfile’,就是自己实现的filter。可以说filters中是所有的过滤器集合。
- filterChains相当于在ini配置中配的urls选项，在此因为我的项目中只配了`/** = anno`，所以在此现实的是如下内容，不过他是把anno翻译成了具体的类。这个对象应该不难理解。
    > 默认的过滤器在枚举类`DefaultFilter`所示。

    ```
    filters = 
        {
            anon=anon, 
            authc=authc, 
            authcBasic=authcBasic, 
            logout=logout, 
            noSessionCreation=noSessionCreation, 
            perms=perms, 
            port=port, 
            rest=rest, 
            roles=roles, 
            ssl=ssl, 
            user=user, 
            myfile=myfile
        }
    ```
    ```
    filterChains = 
        {
            /** = org.apache.shiro.web.filter.mgt.SimpleNamedFilterList@251123c5
        }
    ```
    ```java
    public enum DefaultFilter {

        anon(AnonymousFilter.class),
        authc(FormAuthenticationFilter.class),
        authcBasic(BasicHttpAuthenticationFilter.class),
        logout(LogoutFilter.class),
        noSessionCreation(NoSessionCreationFilter.class),
        perms(PermissionsAuthorizationFilter.class),
        port(PortFilter.class),
        rest(HttpMethodPermissionFilter.class),
        roles(RolesAuthorizationFilter.class),
        ssl(SslFilter.class),
        user(UserFilter.class);
    
        private final Class<? extends Filter> filterClass;
    }
    ```

## PathMatchingFilterChainResolver
`PathMatchingFilterChainResolver`类实现于`FilterChainResolver`接口，可以看出,该对象也是配置Shiro关键的一部分。在ShiroFilter的入口（如下代码所示），通过set方法将WebSecurityManager和FilterChainResolver两个对象注入。

该对象中包含有两个对象，分别是FilterChainManager和PatternMatcher对象。
- FilterChainManager对象主要功能是保存urls中的配置和开发者自己实现filter的部分。
- PatternMatcher对象的功能是匹配逻辑实现。具体点说是当前requestURI与FilterChainManager对象的中filterChains的匹配规则。

```java
private static final class SpringShiroFilter extends AbstractShiroFilter {

    protected SpringShiroFilter(WebSecurityManager webSecurityManager, FilterChainResolver resolver) {
        super();
        if (webSecurityManager == null) {
            throw new IllegalArgumentException("WebSecurityManager property cannot be null.");
        }
        setSecurityManager(webSecurityManager);
        if (resolver != null) {
            setFilterChainResolver(resolver);
        }
    }
}
```
```java
public class PathMatchingFilterChainResolver implements FilterChainResolver {

    private static transient final Logger log = LoggerFactory.getLogger(PathMatchingFilterChainResolver.class);

    private FilterChainManager filterChainManager;

    private PatternMatcher pathMatcher;

    public PathMatchingFilterChainResolver() {
        this.pathMatcher = new AntPathMatcher();
        this.filterChainManager = new DefaultFilterChainManager();
    }

    public PathMatchingFilterChainResolver(FilterConfig filterConfig) {
        this.pathMatcher = new AntPathMatcher();
        this.filterChainManager = new DefaultFilterChainManager(filterConfig);
    }
}

```

# FilterChainResolver --- 加载的全过程

## FilterChainManager的创建过程

```
FilterChainManager manager = createFilterChainManager();
PathMatchingFilterChainResolver chainResolver = new PathMatchingFilterChainResolver();
chainResolver.setFilterChainManager(manager);
```
FilterChainManager 中的filterChains是个map类型，其key为String类型，value为NamedFilterList类型。

NamedFilterList中保存着两个私有属性，name和backingList，name是String类型，backingList为List类型（泛型为Filter）。
通过上面的介绍，咱们给他们分别起个代号。
> key -> String  
n -> String  
bL -> List


如：/** = foo, bar[baz], blah[x, y]

值 | 位置
---|---
/** | key , n
foo | bL[0]  
bar | bL[1]  
blah | bL[2]
/** | (PathMatchingFilter)bL[0]  .  appliedPaths  .  key 
baz | (PathMatchingFilter)bL[1]  .  appliedPaths  .  value
x,y | (PathMatchingFilter)bL[2]  .  appliedPaths  .  value

