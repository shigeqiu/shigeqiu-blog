# BeanDefinitionRegistry


## uml

![ae](../../../img/spring/BeanDefinitionRegistry.png)

上图为**BeanDefinitionRegistry**和**AliasRegistry**的接口、实现类之间的关系

## translate

- BeanDefinitionRegistry : Bean定义注册中心
- AliasRegistry : 别名注册中心

## BeanDefinitionRegistry

| 返回值  | 方法名&参数  |
|---|---|
| `void` | registerBeanDefinition(`String` beanName, `BeanDefinition` beanDefinition) |
| `void` | removeBeanDefinition(`String` beanName) |
| `BeanDefinition` | getBeanDefinition(`String` beanName) |
| `boolean` | containsBeanDefinition(`String` beanName) |
| `String[]` | getBeanDefinitionNames() |
| `int` | getBeanDefinitionCount() |
| `boolean` | isBeanNameInUse(`String` beanName) |


## AliasRegistry

| 返回值  | 方法名&参数  |
|---|---|
| `void` | registerAlias(`String` name, `String` alias) |
| `void` | removeAlias(`String` alias) |
| `boolean` | isAlias(`String` name) |
| `String[]` | getAliases(`String` name) |