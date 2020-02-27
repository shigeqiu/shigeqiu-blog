# BeanFactory

<!-- TOC -->

- [BeanFactory](#beanfactory)
    - [XmlBeanFactory](#xmlbeanfactory)

<!-- /TOC -->

![](../../img/spring/BeanFactory_ApplicationContext.png)

BeanFactory：是IOC容器的核心接口， 它定义了IOC的基本功能。

> BeanFactory 是初始化 Bean 和调用它们生命周期方法的“吃苦耐劳者”。注意，BeanFactory 只能管理单例（Singleton）Bean 的生命周期。它不能管理原型(prototype,非单例)Bean 的生命周期。这是**因为原型 Bean 实例被创建之后便被传给了客户端,容器失去了对它们的引用。**

BeanFactory接口的具体方法：
1. 4个获取实例的方法。getBean的重载方法。
1. 4个判断的方法。判断是否存在，是否为单例、原型，名称类型是否匹配。
1. 1个获取类型的方法、一个获取别名的方法。根据名称获取类型、根据名称获取别名。一目了然！

总结： **这10个方法，很明显，这是一个典型的工厂模式的工厂接口。**

## XmlBeanFactory

BeanFactory最常见的实现类为XmlBeanFactory，可以从classpath或文件系统等获取资源。

1. XmlBeanFactory通过Resource装载Spring配置信息冰启动IoC容器，然后就可以通过`factory.getBean`从IoC容器中获取Bean了。
1. 通过BeanFactory启动IoC容器时，并不会初始化配置文件中定义的Bean，初始化动作发生在第一个调用时。
1. 对于单实例（singleton）的Bean来说，BeanFactory会缓存Bean实例，所以第二次使用getBean时直接从IoC容器缓存中获取Bean。
