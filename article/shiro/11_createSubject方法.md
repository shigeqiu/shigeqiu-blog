# createSubject方法

> SecurityManager 接口中createSubject方法的解析区别

## 方法 -- createSubject(SubjectContext subjectContext)


描述 | DefaultSecurityManager.createSubject() | DefaultWebSubjectFactory.createSubject()
---|---|---
 解析RecurityManager | resolveSecurityManager() | resolveSecurityManager()
 解析session| resolveSession | resolveSession
 如果构造的对象允许创建会话，则返回true，否则将错误。Shiro的配置默认为true，因为大多数应用程序都在会话中找到值。 | - | isSessionCreationEnabled
 解析Principals | resolvePrincipals | resolvePrincipals
解析resolveAuthenticated | - | resolveAuthenticated
解析Host（主要也是判断是否为空） | - | resolveHost
判断是否为空 | - | resolveServletRequest
判断是否为空 | - | resolveServletResponse



