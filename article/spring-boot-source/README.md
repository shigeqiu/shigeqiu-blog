# Spring Boot 源码阅读

## 总览

- [BeanFactory](总览/BeanFactory.md)
- [一切从SpringApplication开始](总览/一切从SpringApplication开始.md)

## ApplicationContext

- [1. ApplicationContext](1_ApplicationContext/1_ApplicationContext.md)
- [2. ConfigurableApplicationContext](1_ApplicationContext/2_ConfigurableApplicationContext.md)
- [3. AbstractApplicationContext.md](1_ApplicationContext/3_AbstractApplicationContext.md)
- [4. AnnotationConfigServletWebServerApplicationContext](1_ApplicationContext/4_AnnotationConfigServletWebServerApplicationContext.md)
- [5. BeanFactory和ApplicationContext](1_ApplicationContext/5_BeanFactory和ApplicationContext.md)

## Listable

- [1. ConfigurableListableBeanFactory](2_listable/1_ConfigurableListableBeanFactory.md)
- [2. DefaultListableBeanFactory](2_listable/2_DefaultListableBeanFactory.md)

## [PostProcessorRegistrationDelegate](4_PostProcessorRegistrationDelegate/README.md)

- [1. invokeBeanFactoryPostProcessors方法](4_PostProcessorRegistrationDelegate/1_invokeBeanFactoryPostProcessors方法.md)
- [2. registerBeanPostProcessors方法](4_PostProcessorRegistrationDelegate/2_registerBeanPostProcessors方法.md)



## BeanFactory Post Processor

- [1. BeanFactoryPostProcessor](BeanFactoryPostProcessor/1_BeanFactoryPostProcessor.md)
- ConfigurationClassPostProcessor
  - [1. ConfigurationClassPostProcessor](5_ConfigurationClassPostProcessor/1_ConfigurationClassPostProcessor.md)
  - [2. ConfigurationClassParser](5_ConfigurationClassPostProcessor/2_ConfigurationClassParser.md)
  - [3. ConfigurationClassBeanDefinitionReader](5_ConfigurationClassPostProcessor/3_ConfigurationClassBeanDefinitionReader.md)
  - [4. ConfigurationClass](5_ConfigurationClassPostProcessor/4_ConfigurationClass.md)
  - [5. ConfigurationClassUtils](5_ConfigurationClassPostProcessor/5_ConfigurationClassUtils.md)



## Bean Post Processor

- [1. BeanPostProcessor](6_post-processor/1_BeanPostProcessor.md)
- [2. ConditionEvaluator](6_post-processor/2_ConditionEvaluator.md)


## Definition

- [1. BeanDefinition](3_definition/1_BeanDefinition.md)
- [2. BeanDefinitionRegistry](3_definition/2_BeanDefinitionRegistry.md)
- [3. BeanMetadataElement](3_definition/3_BeanMetadataElement.md)



## GetBean

- [1. doGetBean](7_getbean/1_do_get_bean.md)
- [2. createBean](7_getbean/2_createBean.md)
- [3. doCreateBean](7_getbean/3_do_create_bean.md)