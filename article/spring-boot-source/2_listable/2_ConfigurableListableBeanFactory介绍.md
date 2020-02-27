# ConfigurableListableBeanFactory介绍

![ae](../../../img/spring/BeanFactory_ConfigurableListableBeanFactory.png)

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