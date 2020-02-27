# AbstractApplicationContext

<!-- TOC -->

- [AbstractApplicationContext](#abstractapplicationcontext)
  - [refresh方法的三个阶段](#refresh%E6%96%B9%E6%B3%95%E7%9A%84%E4%B8%89%E4%B8%AA%E9%98%B6%E6%AE%B5)
  - [refresh方法 try阶段](#refresh%E6%96%B9%E6%B3%95-try%E9%98%B6%E6%AE%B5)
    - [1. prepareRefresh](#1-preparerefresh)
    - [2. obtainFreshBeanFactory](#2-obtainfreshbeanfactory)
    - [3. prepareBeanFactory](#3-preparebeanfactory)
    - [4. postProcessBeanFactory](#4-postprocessbeanfactory)
    - [5. invokeBeanFactoryPostProcessors](#5-invokebeanfactorypostprocessors)
    - [6. registerBeanPostProcessors](#6-registerbeanpostprocessors)
    - [7. initMessageSource](#7-initmessagesource)
    - [8. initApplicationEventMulticaster](#8-initapplicationeventmulticaster)
    - [9. onRefresh](#9-onrefresh)
    - [10. registerListeners](#10-registerlisteners)
    - [11. finishBeanFactoryInitialization](#11-finishbeanfactoryinitialization)
    - [12. finishRefresh](#12-finishrefresh)
  - [定义的抽象方法](#%E5%AE%9A%E4%B9%89%E7%9A%84%E6%8A%BD%E8%B1%A1%E6%96%B9%E6%B3%95)
  - [BeanFactory接口的实现方法](#beanfactory%E6%8E%A5%E5%8F%A3%E7%9A%84%E5%AE%9E%E7%8E%B0%E6%96%B9%E6%B3%95)

<!-- /TOC -->


## refresh方法的三个阶段

1. try阶段
2. catch阶段
3. finallyj阶段

## refresh方法 try阶段

### 1. prepareRefresh

``` java
prepareRefresh();
```
- Prepare this context for refreshing.
- 准备 context，为了刷新

### 2. obtainFreshBeanFactory

``` java
ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();
```
- Tell the subclass to refresh the internal bean factory.
- 告诉subClass(子类) 刷新内部 bean factory

### 3. prepareBeanFactory

``` java
prepareBeanFactory(beanFactory);
```
- Prepare the bean factory for use in this context.
- 准备 bean factory ， 以便在该context中使用

### 4. postProcessBeanFactory

``` java
postProcessBeanFactory(beanFactory);
```
- Allows post-processing of the bean factory in context subclasses.
- 在 context 的多个 subClass 中，允许 bean factory 的 post-processing

### 5. invokeBeanFactoryPostProcessors

```java
invokeBeanFactoryPostProcessors(beanFactory);
```

- Invoke factory processors registered as beans in the context.
- 调用 在 context 中注册为bean 的 factory processors(工厂处理器)(复数) 


**移步 PostProcessorRegistrationDelegate > invokeBeanFactoryPostProcessors方法**

### 6. registerBeanPostProcessors

``` java 
registerBeanPostProcessors(beanFactory);
```
- Register bean processors that intercept bean creation.
- 注册“拦截创造bean”的bean processors。

**移步 PostProcessorRegistrationDelegate > registerBeanPostProcessors方法**

### 7. initMessageSource

- Initialize message source for this context.
- 为该context初始化 message source

### 8. initApplicationEventMulticaster

``` java 
initApplicationEventMulticaster();
```
- Initialize event multicaster for this context.
- 为该context初始化Event Multicaster

### 9. onRefresh

```
onRefresh();
```
- Initialize other special beans in specific context subclasses.
- 在特定的 context subclass 中 初始化其他特殊的 bean。

### 10. registerListeners

```
registerListeners();
```
- Check for listener beans and register them.
- 检查 listener beans 并注册它们。


### 11. finishBeanFactoryInitialization

```
finishBeanFactoryInitialization(beanFactory);
```
- Instantiate all remaining (non-lazy-init) singletons.
- 实例化所有剩余的(非延迟-init)单例。


### 12. finishRefresh

```
finishRefresh();
```
- Last step: publish corresponding event.
- 最后一步:发布相应的事件。

## 定义的抽象方法

| 详情  |
|---|
| `void` `refreshBeanFactory()`
| `void` `closeBeanFactory()`
| `ConfigurableListableBeanFactory` `getBeanFactory()`


## BeanFactory接口的实现方法

对于BeanFactory的实现全部通过`this.getBeanFactory()`方法获取到的ConfigurableListableBeanFactory实例来处理具体的实现逻辑，也就是说委托给了ConfigurableListableBeanFactory。