# ConfigurableListableBeanFactory 方法以及定义的参数

![ae](../../../img/spring/BeanFactory_ConfigurableListableBeanFactory.png)

上图为**BeanFactory**到**ConfigurableListableBeanFactory**的继承关系


| 接口名  | 释义  |
|---|---|
| SingletonBeanRegistry  | 单例Bean Registry(注册中心)  |
| ConfigurableBeanFactory  | 可配置的Bean Factory  |
| ConfigurableListableBeanFactory  | 可配置并且可(列表式)浏览的Bean Factory  |

## SingletonBeanRegistry

| 返回值  | 方法名&参数  |
|---|---|
| void | registerSingleton(String beanName, Object singletonObject) |
| Object | getSingleton(String beanName) |
| boolean | containsSingleton(String beanName) |
| String[] | getSingletonNames() |
| int | getSingletonCount() |
| Object | getSingletonMutex() |

## ConfigurableBeanFactory

| 返回值  | 方法名&参数  |
|---|---|  
| `void` | setParentBeanFactory(`BeanFactory` parentBeanFactory) |
| `void` | setBeanClassLoader(@Nullable `ClassLoader` beanClassLoader) |
| `ClassLoader` | getBeanClassLoader() |
| `void` | setTempClassLoader(@Nullable `ClassLoader` tempClassLoader) |
| `ClassLoader` | getTempClassLoader() |
| `void` | setCacheBeanMetadata(`boolean` cacheBeanMetadata) |
| `boolean` | isCacheBeanMetadata() |
| `void` | setBeanExpressionResolver( `@Nullable` `BeanExpressionResolver` resolver) |
| `BeanExpressionResolver` | getBeanExpressionResolver() |
| `void` | setConversionService(@Nullable `ConversionService` conversionService) |
| `ConversionService` | getConversionService() |
| `void` | addPropertyEditorRegistrar(`PropertyEditorRegistrar` registrar) |
| `void` | registerCustomEditor(`Class<?>` requiredType, `Class<? extends PropertyEditor>` propertyEditorClass) |
| `void` | copyRegisteredEditorsTo(`PropertyEditorRegistry` registry) |
| `void` | setTypeConverter(`TypeConverter` typeConverter) |
| `TypeConverter` | getTypeConverter() |


## ConfigurableListableBeanFactory

| 返回值  | 方法名&参数  |
|---|---|
| `void`  | ignoreDependencyType(`Class<?>` type);  |
| `void`  | ignoreDependencyInterface(`Class<?>` ifc);  |
| `void`  | registerResolvableDependency(`Class<?>` dependencyType, `@Nullable` `Object` autowiredValue);  |
| `boolean`  | isAutowireCandidate(`String` beanName, `DependencyDescriptor` descriptor);  |
| `BeanDefinition` | getBeanDefinition(`String` beanName); |
| `Iterator<String>` | getBeanNamesIterator(); |
| `void` | clearMetadataCache(); |
| `void` | freezeConfiguration(); |
| `boolean` | isConfigurationFrozen(); |
| `void` | preInstantiateSingletons(); |

## 介绍

ConfigurableListableBeanFactory主要实现了Spring中的Bean的管理，从图中分析，我们可以分为以下几层来逐步阅读

- ConfigurableBeanFactory
- AutowireCapableBeanFactory
- ConfigurableListableBeanFactory

## ConfigurableBeanFactory

定义了大量的`set/get`方法，具体实现都在AbstractBeanFactory中完成，下面AbstractBeanFactory中定义的一些内部变量，列出来的一部分代码仅供参考
``` java 
    @Nullable
    private BeanFactory parentBeanFactory;
    @Nullable
    private ClassLoader beanClassLoader = ClassUtils.getDefaultClassLoader();
    @Nullable
    private ClassLoader tempClassLoader;
    private boolean cacheBeanMetadata = true;
    @Nullable
    private BeanExpressionResolver beanExpressionResolver;
    @Nullable
    private ConversionService conversionService;
```