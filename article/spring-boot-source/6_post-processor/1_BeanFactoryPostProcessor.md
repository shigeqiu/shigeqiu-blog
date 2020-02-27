# BeanFactoryPostProcessor

> BeanFactory 后置处理器

<!-- TOC -->

- [BeanFactoryPostProcessor](#beanfactorypostprocessor)
  - [方法](#%E6%96%B9%E6%B3%95)

<!-- /TOC -->


![ae](../../../img/spring/BeanFactoryPostProcessor.png)

上图为**BeanFactoryPostProcessor**与**BeanDefinitionRegistryPostProcessor**接口继承关系

## 方法

BeanFactoryPostProcessor接口定义的方法：**↓↓↓**

```java 
public interface BeanFactoryPostProcessor {

	void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;

}
```

BeanDefinitionRegistryPostProcessor接口定义的方法：**↓↓↓**

``` java
public interface BeanDefinitionRegistryPostProcessor extends BeanFactoryPostProcessor {
    
	void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException;

}
```





