# SecurityManager


<!-- TOC -->

- [SecurityManager](#securitymanager)
- [顶级接口](#顶级接口)
    - [Authenticator（认证器）](#authenticator认证器)
    - [Authorizer（授权器）](#authorizer授权器)
    - [SecurityManager(安全管理器)](#securitymanager安全管理器)
    - [SessionManager（会话管理器）](#sessionmanager会话管理器)
- [SecurityManager](#securitymanager-1)
    - [RealmSecurityManager](#realmsecuritymanager)
    - [AuthenticatingSecurityManager](#authenticatingsecuritymanager)
    - [SessionsSecurityManager](#sessionssecuritymanager)
    - [DefaultSecurityManager](#defaultsecuritymanager)
        - [登录操作的主要逻辑](#登录操作的主要逻辑)
    - [DefaultWebSecurityManager](#defaultwebsecuritymanager)
    - [主要操作类](#主要操作类)
        - [ModularRealmAuthenticator](#modularrealmauthenticator)
- [ThreadContext](#threadcontext)
- [Subject](#subject)
- [Realm](#realm)
    - [AuthenticatingRealm](#authenticatingrealm)
        - [API翻译](#api翻译)
        - [分析](#分析)
    - [IniRealm与TextConfigurationRealm](#inirealm与textconfigurationrealm)
    - [SimpleAccountRealm](#simpleaccountrealm)
- [Session](#session)
    - [ProxiedSession](#proxiedsession)
    - [StoppingAwareProxiedSession](#stoppingawareproxiedsession)
    - [ImmutableProxiedSession](#immutableproxiedsession)
- [SubjectDAO](#subjectdao)
    - [API翻译](#api翻译-1)
    - [DefaultSubjectDAO](#defaultsubjectdao)
        - [API翻译](#api翻译-2)

<!-- /TOC -->



![Factory](../../img/shiro/Manager.png)

# 顶级接口


## Authenticator（认证器）

An Authenticator is responsible for authenticating accounts in an application. It is one of the primary entry points into the Shiro API.

在一个应用里边，一个 Authenticator（认证器） 专门负责认证用户。并且它是进入Shiro API操作的一个主要入口。


## Authorizer（授权器）


An Authorizer performs authorization (access control) operations for any given Subject (aka 'application user').

一个Authorizer（授权人）为所有的Subject（又名“应用程序的用户”）执行授权操作（访问控制）。


## SecurityManager(安全管理器)

A SecurityManager executes all security operations for all Subjects (aka users) across a single application.

一个SecurityManager为所有Subjects对象（即用户）执行所有的安全操作，在一个单例的程序里边。

## SessionManager（会话管理器）

A SessionManager（session管理） manages the creation, maintenance, and clean-up of all application Sessions.

一个SessionManager主要负责创造，维护，和所有的应用程序会话清理。
# SecurityManager 

## RealmSecurityManager

`setRealm()`方法的功能描述：
1. 将Realm集合赋值给内部的私有成员变量realms
2. 走`afterRealmsSet()`方法，将`CacheManager`对象注入到`Realm`集合中;
3. `afterRealmsSet()`由子类一一继承，然后由上至下依次执行。
4. 方法调用结束

```java
public abstract class RealmSecurityManager extends CachingSecurityManager {
    private Collection<Realm> realms;
    
    public void setRealm(Realm realm) {
        if (realm == null) {
            throw new IllegalArgumentException("Realm argument cannot be null");
        }
        Collection<Realm> realms = new ArrayList<Realm>(1);
        realms.add(realm);
        setRealms(realms);
    }
    
    public void setRealms(Collection<Realm> realms) {
        if (realms == null) {
            throw new IllegalArgumentException("Realms collection argument cannot be null.");
        }
        if (realms.isEmpty()) {
            throw new IllegalArgumentException("Realms collection argument cannot be empty.");
        }
        this.realms = realms;
        afterRealmsSet();
    }
    
    protected void applyCacheManagerToRealms() {
        CacheManager cacheManager = getCacheManager();
        Collection<Realm> realms = getRealms();
        if (cacheManager != null && realms != null && !realms.isEmpty()) {
            for (Realm realm : realms) {
                if (realm instanceof CacheManagerAware) {
                    ((CacheManagerAware) realm).setCacheManager(cacheManager);
                }
            }
        }
    }
    
    protected void afterRealmsSet() {
        applyCacheManagerToRealms();
    }
    
}
```
AuthenticatingSecurityManager类中维护着一个authenticator中，通过重写`afterRealmsSet()`方法，将realms注入进去。

```java
public abstract class AuthenticatingSecurityManager extends RealmSecurityManager {

    private Authenticator authenticator;
    
    protected void afterRealmsSet() {
        super.afterRealmsSet();
        if (this.authenticator instanceof ModularRealmAuthenticator) {
            ((ModularRealmAuthenticator) this.authenticator).setRealms(getRealms());
        }
    }
}
```
AuthorizingSecurityManager类中维护着一个authorizer中，通过重写`afterRealmsSet()`方法，将realms注入进去。
```java
public abstract class AuthorizingSecurityManager extends  AuthenticatingSecurityManager{

    private Authorizer authorizer;
    
    protected void afterRealmsSet() {
        super.afterRealmsSet();
        if (this.authorizer instanceof ModularRealmAuthorizer) {
            ((ModularRealmAuthorizer) this.authorizer).setRealms(getRealms());
        }
    }

}
```

## AuthenticatingSecurityManager

```java
    /**
     * The internal <code>Authenticator</code> delegate instance that this SecurityManager instance will use
     * to perform all authentication operations.
     */
    private Authenticator authenticator;
    
    /**
     * Delegates to the wrapped {@link org.apache.shiro.authc.Authenticator Authenticator} for authentication.
     */
    public AuthenticationInfo authenticate(AuthenticationToken token) throws AuthenticationException {
        return this.authenticator.authenticate(token);
    }
```
AuthenticatingSecurityManager认证过程委托给他的内部成员；去执行所有的认证操作


## SessionsSecurityManager

内部维护着一个`SessionManager`对象

## DefaultSecurityManager

The Shiro framework's default concrete implementation of the SecurityManager interface, based around a collection of Realms. This implementation delegates its authentication, authorization, and session operations to wrapped Authenticator, Authorizer, and SessionManager instances respectively via superclass implementation.

这个Shiro框架中基于Realms的集合并且实现SecurityManager接口的具体的一个默认类。它包括认证，授权，和包括认证，授权的回话操作。并且是实现SessionManager的一个实例。


```java
public class DefaultSecurityManager extends SessionsSecurityManager {
    protected RememberMeManager rememberMeManager;
    protected SubjectDAO subjectDAO;
    protected SubjectFactory subjectFactory;

    /**
     * Default no-arg constructor.
     */
    public DefaultSecurityManager() {
        super();
        this.subjectFactory = new DefaultSubjectFactory();
        this.subjectDAO = new DefaultSubjectDAO();
    }
    
}
```

一个DefaultSecurityManager对象包含以下多个属性。
```java
public class DefaultSecurityManager{
/**自身属性*/
protected RememberMeManager rememberMeManager;
protected SubjectDAO subjectDAO;
protected SubjectFactory subjectFactory;
/**SessionsSecurityManager中*/
private SessionManager sessionManager;
/**AuthorizingSecurityManager*/
private Authorizer authorizer;
/**AuthenticatingSecurityManager*/
private Authenticator authenticator;
/**RealmSecurityManager*/
private Collection<Realm> realms;
/**CachingSecurityManager*/
private CacheManager cacheManager;
}

```
根据token，info，subject创建Subject的过程。

```
public class DefaultSecurityManager{

protected Subject createSubject(AuthenticationToken token, AuthenticationInfo info, Subject existing) {
        SubjectContext context = createSubjectContext();
        context.setAuthenticated(true);
        context.setAuthenticationToken(token);
        context.setAuthenticationInfo(info);
        if (existing != null) {
            context.setSubject(existing);
        }
        return createSubject(context);
    }


public Subject createSubject(SubjectContext subjectContext) {
        //create a copy so we don't modify the argument's backing map:
        SubjectContext context = copy(subjectContext);

        //ensure that the context has a SecurityManager instance, and if not, add one:
        context = ensureSecurityManager(context);

        //Resolve an associated Session (usually based on a referenced session ID), and place it in the context before
        //sending to the SubjectFactory.  The SubjectFactory should not need to know how to acquire sessions as the
        //process is often environment specific - better to shield the SF from these details:
        context = resolveSession(context);

        //Similarly, the SubjectFactory should not require any concept of RememberMe - translate that here first
        //if possible before handing off to the SubjectFactory:
        context = resolvePrincipals(context);

        Subject subject = doCreateSubject(context);

        //save this subject for future reference if necessary:
        //(this is needed here in case rememberMe principals were resolved and they need to be stored in the
        //session, so we don't constantly rehydrate the rememberMe PrincipalCollection on every operation).
        //Added in 1.2:
        save(subject);

        return subject;
    }
}

```
```
public class DefaultSubjectFactory implements SubjectFactory {

    public DefaultSubjectFactory() {
    }

    public Subject createSubject(SubjectContext context) {
        SecurityManager securityManager = context.resolveSecurityManager();
        Session session = context.resolveSession();
        boolean sessionCreationEnabled = context.isSessionCreationEnabled();
        PrincipalCollection principals = context.resolvePrincipals();
        boolean authenticated = context.resolveAuthenticated();
        String host = context.resolveHost();

        return new DelegatingSubject(principals, authenticated, host, session, sessionCreationEnabled, securityManager);
    }
```

### 登录操作的主要逻辑
有以下几步:
1. 根据token获取到info
2. 由info、token、subject创建一个新的subject
3. `RememberMeManager`对象的操作。
4. 返回subject对象

```
public class DefaultSubjectFactory implements SubjectFactory {
    public Subject login(Subject subject, AuthenticationToken token) throws AuthenticationException {
        AuthenticationInfo info;
        try {
            info = authenticate(token);
        } catch (AuthenticationException ae) {
            try {
                onFailedLogin(token, ae, subject);
            } catch (Exception e) {
                if (log.isInfoEnabled()) {
                    log.info("onFailedLogin method threw an " +
                            "exception.  Logging and propagating original AuthenticationException.", e);
                }
            }
            throw ae; //propagate
        }

        Subject loggedIn = createSubject(token, info, subject);

        onSuccessfulLogin(token, info, loggedIn);

        return loggedIn;
    }
}
```

## DefaultWebSecurityManager

> Default {@link WebSecurityManager WebSecurityManager} implementation used in web-based applications or any
 application that requires HTTP connectivity (SOAP, http remoting, etc).
 
 > 默认{@link WebSecurityManager WebSecurityManager }实现使用的基于Web的应用程序或者其他
需要HTTP连接（SOAP，HTTP远程处理，等）的应用程序。


## 主要操作类

### ModularRealmAuthenticator
类中包含两个私有成员，分别是`Collection<Realm>`，`AuthenticationStrategy`。验证身份的主要逻辑是调用的其父类`AbstractAuthenticator `的`authenticate(AuthenticationToken token)`方法，该方法再委托给`ModularRealmAuthenticator`去执行。所以验证的主要逻辑还是走的该实类的（也是主要的实现类）`doAuthenticate`方法。最后`doAuthenticate`方法会通过判断realm的个数来确定最后的返回值,`AuthenticationStrategy`就是干(判断realm个数)这个事情的。

```java
public class ModularRealmAuthenticator extends AbstractAuthenticator {

    private Collection<Realm> realms;
    
    private AuthenticationStrategy authenticationStrategy;
    
    

    protected AuthenticationInfo doAuthenticate(AuthenticationToken authenticationToken) throws AuthenticationException {
        assertRealmsConfigured();
        Collection<Realm> realms = getRealms();
        if (realms.size() == 1) {
            return doSingleRealmAuthentication(realms.iterator().next(), authenticationToken);
        } else {
            return doMultiRealmAuthentication(realms, authenticationToken);
        }
    }


    protected AuthenticationInfo doSingleRealmAuthentication(Realm realm, AuthenticationToken token) {
        if (!realm.supports(token)) {
            String msg = "Realm [" + realm + "] does not support authentication token [" +
                    token + "].  Please ensure that the appropriate Realm implementation is " +
                    "configured correctly or that the realm accepts AuthenticationTokens of this type.";
            throw new UnsupportedTokenException(msg);
        }
        AuthenticationInfo info = realm.getAuthenticationInfo(token);
        if (info == null) {
            String msg = "Realm [" + realm + "] was unable to find account data for the " +
                    "submitted AuthenticationToken [" + token + "].";
            throw new UnknownAccountException(msg);
        }
        return info;
    }
}

```


# ThreadContext

> A ThreadContext provides a means of binding and unbinding objects to the
 current thread based on key/value pairs.
 
> 一个ThreadContext(线程上下文)以key/value对形式提供绑定与取消绑定对象到当前线程的方法。


# Subject 


`getSession`方法介绍：

```java
Session getSession(boolean create);
```

> 返回与此Subject相关的应用程序会话。基于布尔参数，该方法的功能如下
> - 如果已经有与这个Subject相关的现有会话，则返回它，而`create`参数将被忽略。
> - 如果没有会话存在并且`create = true`，将创建一个与这个主题相关联的新会话，然后返回。
> - 如果没有会话存在并且`create = false`，则返回null。





# Realm
- `getName()`方法返回一个赋给该realm的（应用程序中唯一）名字，所有配置在一个应用程序中的realms必须有一个唯一的名字
。
- `supports`方法，通过给定的AuthenticationToken实例，如果这个realm希望验证subject主体就返回true，否则为false。
- `getAuthenticationInfo`方法，通过参数给定的token返回账户具体的验证身份信息，如果没有则返回空。
```java
 public interface Realm {
 
    String getName();
    
    boolean supports(AuthenticationToken token);
    
    AuthenticationInfo getAuthenticationInfo(AuthenticationToken token) throws AuthenticationException;
}
```

## AuthenticatingRealm

### API翻译 

> A top-level abstract implementation of the Realm interface that only implements authentication support (log-in) operations and leaves authorization (access control) behavior to subclasses.

> Realm接口的顶级抽象实现，只实现身份验证支持（登录）操作，并将授权（访问控制）行为留给子类。

- 认证缓存

对于经常重复验证同一帐户的应用程序，(例如，在REST或Soap应用程序中常常对每个请求进行身份验证），启用身份验证缓存以减轻任何后端数据源上的恒定负载，这或许是明智的一种做法。


这个功能在默认情况下是禁用的（这个特性是保留1.1和更早的Shiro的并具有向后兼容性），可以通过设置` authenticationCachingEnabled = true`来启用，**==请注意:==**

- **==对于Realm的实现启用认证缓存，只需要满足以下两个的任意一项:==**
1. 通过实现`doGetAuthenticationInfo`方法返回`AuthenticationInfo`实例，`AuthenticationInfo`的`credentials`通过安全的打乱并且`credentials`不能是明文(raw)，例如，如果你的realm以账号和密码为准，那对于AuthenticationInfo的`credentials`是安全散列（hashed），否则是完全加密。
2. 通过实现`doGetAuthenticationInfo`方法返回`AuthenticationInfo`实例，`AuthenticationInfo`的`credentials`是明文(raw)**==并且==** 将它保存到缓存区，`AuthenticationInfo`的实例不能溢出到磁盘并且不能将缓存项发送在一个不受保护的(非TLS / SSL)网络(可能是网络/分布式企业缓存)的情况。应该将它放在私有/可信/企业网络中。

这些点非常重要，因为如果启用了authentication（身份验证） 缓存，那么这个抽象类实现将把从子类实现返回的AuthenticationInfo实例直接返回到缓存中，例如:

```
cache.put(cacheKey, subclassAuthenticationInfoInstance);
```
启用身份验证缓存只有在上述两个场景适用时才安全。在任何其他场景下都是不安全的。

在可能的情况下，始终以安全的形式(散列加盐或加密)来表示和存储凭证，以消除纯文本可见性。

- 退出后Authentication缓存失效

如果启用身份验证缓存，这个抽象的实现类(也就是AuthenticatingRealm)将尝试在注销期间将缓存的身份验证数据删除。这只能发生在`getAuthenticationCacheKey(org.apache.shiro.authc.AuthenticationToken)`和` getAuthenticationCacheKey(org.apache.shiro.subject.PrincipalCollection)`两个方法返回完全相同的值。

这些方法的默认实现期望在登录时使用`AuthenticationToken.getPrincipal()`(用户在登录时提交的内容)和`getAvailablePrincipal`(在帐户查找后通过Realm返回)返回相同的准确值。例如，用户提交的用户名也是主帐户标识符。

然而,如果您的应用程序使用的是最终用户登录的用户名,但是在身份验证之后返回主键ID，那么您将需要重写`getAuthenticationCacheKey(token)`或`getAuthenticationCacheKey(principals)`，以确保相同的缓存键可以用于任何对象。

这保证在验证过程中用于缓存数据的缓存密钥(从`AuthenticationToken`派生而来)将用于在注销期间删除缓存的数据(从`PrincipalCollection`中派生出来的)。保证秘钥的一致性。

- Cache Key Values 不匹配
如果
`getAuthenticationCacheKey(org.apache.shiro.authc.AuthenticationToken)`和` getAuthenticationCacheKey(org.apache.shiro.subject.PrincipalCollection) `的返回值不一致。缓存的身份验证数据删除是由缓存提供者设置的。例如，通常缓存实现会根据timeToIdle 或timeToLive (TTL)值来删除缓存条目。


如果缓存删除太慢，您需要离散行为(高度推荐)，确保在子类实现中这两个方法的返回值是相同的。

### 分析

主要实现了父类`getAuthenticationInfo`方法。并委托给子类去实现`doGetAuthenticationInfo(token);`方法;


```
public abstract class AuthenticatingRealm extends CachingRealm implements Initializable {

    public final AuthenticationInfo getAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {

        AuthenticationInfo info = getCachedAuthenticationInfo(token);
        if (info == null) {
            //otherwise not cached, perform the lookup:
            info = doGetAuthenticationInfo(token);
            log.debug("Looked up AuthenticationInfo [{}] from doGetAuthenticationInfo", info);
            if (token != null && info != null) {
                cacheAuthenticationInfoIfPossible(token, info);
            }
        } else {
            log.debug("Using cached authentication info [{}] to perform credentials matching.", info);
        }

        if (info != null) {
            assertCredentialsMatch(token, info);
        } else {
            log.debug("No AuthenticationInfo found for submitted AuthenticationToken [{}].  Returning null.", token);
        }

        return info;
    }
    
    protected abstract AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException;
    
}
```
获取info对象的`getAuthenticationInfo`方法主要四步：
1. 去缓存获取 -> `getCachedAuthenticationInfo(token);`
1. 缓存没有，则去子类获取，子类需要实现 -> `doGetAuthenticationInfo(token);`
1. 获取到info存入缓存 -> `cacheAuthenticationInfoIfPossible(token, info);`,缓存的具体逻辑，都在此方法中。
1. 去比对（断言）token与info是否一致 -> `assertCredentialsMatch(token, info);`
1. 返回info


## IniRealm与TextConfigurationRealm

> 这两个类仅用作初始化，为`SimpleAccountRealm`对象服务。

> 这两个realm主要是根据IniRealm中的Ini对象来生成SimpleAccountRealm对象中的users和roles散列。

职责分工如下：
1. `IniRealm`主要负责解析Ini对象，解析成Map类型的值，供`TextConfigurationRealm`使用
2. `TextConfigurationRealm`主要负责解析子类提供的Map，最终给父类`SimpleAccountRealm`的users和roles赋值

## SimpleAccountRealm
> 该类主要功能是进行验证和授权等操作。真正意义上的使用操作类。

# Session

## ProxiedSession
> Proxied 代理、代理人

这是一个专门用作代理的session类，内部有一个代理的实例(也就是专门做事情的实例)。
```java
public class ProxiedSession implements Session {
    protected final Session delegate;
}
```
## StoppingAwareProxiedSession
该类内部仅有一个`DelegatingSubject`实例，
该类主要功能有两个：
1. 调用父类代理的实例`Session`中的`stop()`方法。
2. 调用`DelegatingSubject`实例中的`sessionStopped()`方法。
```
private class StoppingAwareProxiedSession extends ProxiedSession {

        private final DelegatingSubject owner;

        private StoppingAwareProxiedSession(Session target, DelegatingSubject owningSubject) {
            super(target);
            owner = owningSubject;
        }

        public void stop() throws InvalidSessionException {
            super.stop();
            owner.sessionStopped();
        }
    }
```
## ImmutableProxiedSession
> Immutable 不变的，不可变的，不能变的

此类一旦初始化赋值之后就不可变，`set`方法都会抛出异常，运行下面这个方法。
```
protected void throwImmutableException() throws InvalidSessionException {
    String msg = "This session is immutable and read-only - it cannot be altered.  This is usually because " +
            "the session has been stopped or expired already.";
    throw new InvalidSessionException(msg);
}
```

# SubjectDAO
## API翻译
> SubjectDAO负责持久化一个Subject实例的内部状态，这样，如果必要的话，可以在稍后的时间再现Subject实例。

Shiro的默认SecurityManager实现通常会用SubjectDAO与SubjectFactory结合:在SubjectFactory创建一个Subject实例之后，SubjectDAO用来持久化Subject实例，这样在必要时，可以访问它。

## DefaultSubjectDAO

### API翻译 

> 将subject的状态(它的主体和身份验证状态)保存到它的会话。Subject实例可以在稍后的时间重新创建，首先通过会话ID或会话密钥获取相关的会话(通常来自一个SessionManager)，然后从会话属性中构建一个Subject实例。

__控制Sessions的使用方式__

不管一个主题的会话是否被使用或者是否保存它自己的状态，它都是由配置的sessionStorageEvaluator所决定的每个subject来控制的。默认的Evaluator(计算器)是`DefaultSessionStorageEvaluator`，它支持在所有subject的全局级别上启用或禁用主题持久性的会话使用(以及允许使用会话的默认值)。

**完全禁用Session持久性**
 
 
 因为默认的SessionStorageEvaluator 实例是一个DefaultSessionStorageEvaluator, 您可以通过直接配置这个实例来完全禁用Subject状态的会话使用，
 
 ```java
 ((DefaultSessionStorageEvaluator)subjectDAO.getSessionStorageEvaluator()).setSessionStorageEnabled(false);
 ```

但是注意:只有这样，您的应用程序是100%无状态的，并且您不需要在远程调用或在跨HTTP请求的web环境中被记住的Subject。

**支持状态和无状态的Subject范例**

也许您的应用程序需要支持有状态和无状态Subject的混合方法:
- 有状态的:有状态的Subject可能代表web最终用户，他们需要他们的身份和身份验证状态从页面到页面。
- 无状态:无状态的Subject可能表示API客户端(例如REST客户端)对每个请求进行身份验证，因此不需要在会话中存储跨请求的身份验证状态。

为了支持混合的每个主题的方法，您需要创建您自己的SessionStorageEvaluator接口的实现，并通过`setSessionStorageEvaluator(SessionStorageEvaluator)`方法来配置它，或者使用shiro.ini:
```
myEvaluator = com.my.CustomSessionStorageEvaluator
securityManager.subjectDAO。sessionStorageEvaluator = $ myEvaluator
```
除非被覆盖，默认的评估者是一个DefaultSessionStorageEvaluator，它允许在默认情况下对Subject状态进行会话使用。

1. 方法`public Subject save(Subject subject)`说明：  
仅当`isSessionStorageEnabled(subject) = true`时，将主题的状态保存到subject的会话。如果对特定的Subject不启用会话存储，则该方法不执行任何操作。
在这两种情况下，参数Subject直接返回(没有创建新的Subject实例)。

2. 方法`protected void saveToSession(Subject subject)`:  
将subject的状态(它的主体和身份验证状态)保存到它的会话。会话可以在稍后的时间中检索(通常由一个SessionManager来重新创建Subject实例。

3. 