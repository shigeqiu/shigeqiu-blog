# RememberMeManager

<!-- TOC -->

- [RememberMeManager](#remembermemanager)
    - [AbstractRememberMeManager](#abstractremembermemanager)
    - [CookieRememberMeManager](#cookieremembermemanager)

<!-- /TOC -->

该接口声明了五个方法：

 | 方法
 |---
 | PrincipalCollection getRememberedPrincipals(SubjectContext subjectContext);
 | void forgetIdentity(SubjectContext subjectContext);
 | void onSuccessfulLogin(Subject subject, AuthenticationToken token, AuthenticationInfo info);
 | void onFailedLogin(Subject subject, AuthenticationToken token, AuthenticationException ae);
 | void onLogout(Subject subject);



## AbstractRememberMeManager

> 对记住的用户标识的序列化和加密的的抽象实现，记住的身份存储位置和细节留给子类。


## CookieRememberMeManager
> 记住一个主题的身份，将主题的主体保存到一个Cookie中，以便以后检索。

默认的属性：
Attribute Name | Value
---|---
name | rememberMe
path | /
maxAge | Cookie.ONE_YEAR



