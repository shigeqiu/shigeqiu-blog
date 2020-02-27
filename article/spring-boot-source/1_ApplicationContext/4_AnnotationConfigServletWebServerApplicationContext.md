# AnnotationConfigServletWebServerApplicationContext


![ae](../../../img/spring/AnnotationConfigServletWebServerApplicationContext.png)

上图为**AnnotationConfigServletWebServerApplicationContext**继承关系


## 内置对象

| AnnotationConfigServletWebServerApplicationContext对象  |
|---|
| `final AnnotatedBeanDefinitionReader` reader
| `final ClassPathBeanDefinitionScanner` scanner
| `final Set<Class<?>>` annotatedClasses
| `String[]` basePackages


| ServletWebServerApplicationContext对象  |
|---|
| `volatile` `WebServer` webServer
| `ServletConfig` servletConfig
| `String` serverNamespace


| GenericWebApplicationContext对象  |
|---|
| `ServletContext` servletContext
| `ThemeSource` themeSource


| GenericApplicationContext对象  |
|---|
| `final` `DefaultListableBeanFactory` beanFactory
| `ResourceLoader` resourceLoader
| `final` `AtomicBoolean` refreshed


| AbstractApplicationContext对象  |
|---|
| `ApplicationContext` parent
| `ApplicationEventMulticaster` applicationEventMulticaster
| `Set<ApplicationEvent>` earlyApplicationEvents
| `final Set<ApplicationListener<?>>` applicationListeners
| `final List<BeanFactoryPostProcessor>` beanFactoryPostProcessors
| `LifecycleProcessor` lifecycleProcessor
| `MessageSource` messageSource
| `ResourcePatternResolver` resourcePatternResolver
| `ConfigurableEnvironment` environment
| `final AtomicBoolean` active
| `final AtomicBoolean` closed
