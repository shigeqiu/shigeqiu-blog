# doGetBean

Return an instance, which may be shared or independent, of the specified bean.

返回指定 bean 的实例，该实例可以是共享的，也可以是独立的。


<!-- TOC -->

- [doGetBean](#dogetbean)
  - [位置](#%e4%bd%8d%e7%bd%ae)
  - [参数返回值](#%e5%8f%82%e6%95%b0%e8%bf%94%e5%9b%9e%e5%80%bc)
  - [方法体](#%e6%96%b9%e6%b3%95%e4%bd%93)

<!-- /TOC -->

## 位置

``` java
public abstract class AbstractBeanFactory extends FactoryBeanRegistrySupport implements ConfigurableBeanFactory {
    protected <T> T doGetBean(final String name, @Nullable final Class<T> requiredType,
			@Nullable final Object[] args, boolean typeCheckOnly) throws BeansException {
    }

}
```


## 参数返回值

**name** : the name of the bean to retrieve , 要检索的bean的名称 .

**requiredType** : 
the required type of the bean to retrieve , 要检索的bean的必需类型 .

**args** : arguments to use when creating a bean instance using explicit arguments (only applied when creating a new instance as opposed to retrieving an existing one) , 使用显式参数创建bean实例时使用的参数（仅适用于创建新实例而不是检索现有实例） .

**typeCheckOnly** : whether the instance is obtained for a type check, not for actual use , 是否为类型检查而不是实际使用而获取实例 .

**return** : an instance of the bean , 返回一个bean实例 . 

**异常BeansException** : if the bean could not be created , 如果无法创建bean则跑出该异常 .

## 方法体

解析beanName , 如果存在工厂引用前缀则去掉，并将别名解析为规范名称。

Eagerly check singleton cache for manually registered singletons. 提前去检查缓存中是否存在单例bean，如果没有则去注册单例bean，并且返回bean实例。检查的缓存集合如下所示：


| DefaultSingletonBeanRegistry(类名)  | 字段名 | 
|---|---|
| `final Map<String, Object>` |  singletonObjects
| `final Map<String, ObjectFactory<?>>` | singletonFactories
| `final Map<String, Object>` |  earlySingletonObjects

Check if bean definition exists in this factory. 检查在parent factory 中是否存在该bean的定义，如果存在则从parent facotry 中获取。

根据 `typeCheckOnly` 判断 ， Mark the specified bean as already created (or about to be created).
This allows the bean factory to optimize its caching for repeated creation of the specified bean. 将指定的bean标记为已创建（或即将创建）。
这允许bean工厂优化其缓存以重复创建指定的bean。涉及到的集合如下所示：

类 | 类型  | 字段名 | 
---|---| --- | 
AbstractBeanFactory | `final Map<String, RootBeanDefinition>` | mergedBeanDefinitions
AbstractBeanFactory | `final Set<String>` | alreadyCreated

获取`RootBeanDefinition mbd`变量，Return a merged RootBeanDefinition, traversing the parent bean definition if the specified bean corresponds to a child bean definition.返回合并的 RootBeanDefinition ，如果指定的bean对应于子bean定义，则遍历父bean定义。相关的集合如下：

类 | AbstractBeanFactory  | 字段名 | 
---|---| --- | 
AbstractBeanFactory | `Map<String, RootBeanDefinition>` | mergedBeanDefinitions
DefaultListableBeanFactory | `Map<String, BeanDefinition>` | beanDefinitionMap

Guarantee initialization of beans that the current bean depends on.保证当前bean依赖的bean初始化。

Create bean instance. 创建bean实例

Check if required type matches the type of the actual bean instance. 检查 **所需类型 (requiredType)** 是否与实际bean实例的类型匹配。
