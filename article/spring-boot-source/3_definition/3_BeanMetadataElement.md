# BeanMetadataElement


## uml

![ae](../img/BeanMetadataElement_Mergeable.png)

上图为**BeanMetadataElement**和**Mergeable**两大接口、实现类之间的关系

![ae](../img/BeanMetadataElement.png)

上图为**BeanMetadataElement**接口、实现类之间的关系

![ae](../img/BeanMetadataElement_ComponentDefinition.png)

上图为**BeanMetadataElement**和**ComponentDefinition**两大接口、实现类之间的关系

## translate

- BeanMetadataElement : Bean元数据元素
- mergeable ： 可合并

## BeanDefinitionHolder

以下是类的成员变量可见性均为**private** **↓↓↓**

| 修饰符 | 类型  | 变量名 | 默认值
---|---|---|---
| final | BeanDefinition | beanDefinition | 初始化赋值
| final | String | beanName | 初始化赋值
| final | String[] | aliases | 初始化赋值