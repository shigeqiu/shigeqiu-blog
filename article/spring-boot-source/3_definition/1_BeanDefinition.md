# BeanDefinition

<!-- TOC -->

- [BeanDefinition](#beandefinition)
  - [uml](#uml)
  - [translate](#translate)
  - [AttributeAccessorSupport](#attributeaccessorsupport)
  - [BeanMetadataAttributeAccessor](#beanmetadataattributeaccessor)
  - [AbstractBeanDefinition](#abstractbeandefinition)
  - [RootBeanDefinition](#rootbeandefinition)

<!-- /TOC -->

## uml

![ae](../img/BeanDefinition.png)

上图为**BeanDefinition顶级接口**继承关系


![ae](../img/BeanDefinition实现类.png)

上图为**BeanDefinition顶级接口**的**主要实现类**的继承关系

## translate

- BeanDefinition : Bean定义
- AttributeAccessorSupport ：属性访问器支持
- BeanMetadataAttributeAccessor ：Bean元数据属性访问器


## AttributeAccessorSupport

内置对象：
``` java
/** Map with String keys and Object values */
private final Map<String, Object> attributes = new LinkedHashMap<>(0);
```

## BeanMetadataAttributeAccessor

内置对象：
``` java
private Object source;
```


## AbstractBeanDefinition

以下的内置对象可见性均为**private** **↓↓↓**

 修饰符 | 类型  | 变量名 | 默认值
---|---|---|---
 volatile | Object | beanClass | 
| | String |  scope | SCOPE_DEFAULT
|  | boolean | abstractFlag | false
|   | boolean |  lazyInit | false
|  | int | autowireMode |  AUTOWIRE_NO
|  | int | dependencyCheck |  DEPENDENCY_CHECK_NONE
|  | String[] | dependsOn |  
|  | boolean | autowireCandidate |  true
|  | boolean | primary |  false
| final | `Map<String, AutowireCandidateQualifier>` | qualifiers | `new LinkedHashMap<>(0)` 
|  | `Supplier<?>` | instanceSupplier |  
|  | boolean | nonPublicAccessAllowed |  true
|  | boolean | lenientConstructorResolution |  true
|  | String | factoryBeanName |  
|  | String | factoryMethodName |  
|  | ConstructorArgumentValues | constructorArgumentValues |  
|  | MutablePropertyValues | propertyValues |  
|  | MethodOverrides | methodOverrides |  
|  | String | initMethodName |  
|  | String | destroyMethodName |  
|  | boolean | enforceInitMethod |  true
|  | boolean | enforceDestroyMethod |  true
|  | boolean | synthetic |  false
|  | int | role |  BeanDefinition.ROLE_APPLICATION
|  | String | description |  
|  | Resource | resource |  

以下常量的修饰符均为**public static final** **↓↓↓**

 类型  | 变量名 | 默认值
---|---|---|---
| String | SCOPE_DEFAULT | `""` |  
| int | AUTOWIRE_NO | AutowireCapableBeanFactory.AUTOWIRE_NO   
| int | AUTOWIRE_BY_NAME | AutowireCapableBeanFactory.AUTOWIRE_BY_NAME   
| int | AUTOWIRE_BY_TYPE |  AutowireCapableBeanFactory.AUTOWIRE_BY_TYPE
| int | AUTOWIRE_CONSTRUCTOR |  AutowireCapableBeanFactory.AUTOWIRE_CONSTRUCTOR
| int | AUTOWIRE_AUTODETECT |  AutowireCapableBeanFactory.AUTOWIRE_AUTODETECT
| int | DEPENDENCY_CHECK_NONE |  0
| int | DEPENDENCY_CHECK_OBJECTS |  1
| int | DEPENDENCY_CHECK_SIMPLE |  2
| int | DEPENDENCY_CHECK_ALL |  3
| String | INFER_METHOD |  `"(inferred)"`

## RootBeanDefinition

以下是类的成员变量 **↓↓↓**

可见性 | 修饰符 | 类型  | 变量名 | 默认值
---|---|---|---|---
| private |  | BeanDefinitionHolder | decoratedDefinition | 
| private |  | AnnotatedElement | qualifiedElement | 
|  |  | boolean | allowCaching | true 
|  | volatile | ResolvableType | targetType | 
|  | volatile | `Class<?>` | resolvedTargetType | 
|  | volatile | ResolvableType | factoryMethodReturnType | 
|  | final | Object | constructorArgumentLock | `new Object()`
|  |  | Executable | resolvedConstructorOrFactoryMethod | 
|  |  | boolean | constructorArgumentsResolved | false
|  |  | Object[] | resolvedConstructorArguments | 
|  |  | Object[] | preparedConstructorArguments | 
|  | final | Object | postProcessingLock | new Object()
|  |  | boolean | postProcessed | false
|  | volatile | Boolean | beforeInstantiationResolved | 
| private |  | `Set<Member>` | externallyManagedConfigMembers | 
| private |  | `Set<String>` | externallyManagedInitMethods | 
| private |  | `Set<String>` | externallyManagedDestroyMethods | 