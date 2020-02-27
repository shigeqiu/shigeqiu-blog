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
- [2. ConfigurableListableBeanFactory介绍](2_listable/2_ConfigurableListableBeanFactory介绍.md)
- [3. DefaultListableBeanFactory](2_listable/3_DefaultListableBeanFactory.md)

## [PostProcessorRegistrationDelegate](4_PostProcessorRegistrationDelegate)

- [1. invokeBeanFactoryPostProcessors方法](4_PostProcessorRegistrationDelegate/1_invokeBeanFactoryPostProcessors方法.md)
- [2. registerBeanPostProcessors方法](4_PostProcessorRegistrationDelegate/2_registerBeanPostProcessors方法.md)

## Definition

- [1. BeanDefinition](3_definition/1_BeanDefinition.md)
- [2. BeanDefinitionRegistry](3_definition/2_BeanDefinitionRegistry.md)
- [3. BeanMetadataElement](3_definition/3_BeanMetadataElement.md)

## Post Processor

- [1. BeanFactoryPostProcessor](1_BeanFactoryPostProcessor.md)
- [2. BeanPostProcessor](2_BeanPostProcessor.md)
- [3. ConditionEvaluator](4_ConditionEvaluator.md)

## ConfigurationClassPostProcessor

- [1. ConfigurationClassPostProcessor](5_ConfigurationClassPostProcessor/1_ConfigurationClassPostProcessor.md)
- [2. ConfigurationClassParser](5_ConfigurationClassPostProcessor/2_ConfigurationClassParser.md)
- [3. ConfigurationClassBeanDefinitionReader](5_ConfigurationClassPostProcessor/3_ConfigurationClassBeanDefinitionReader.md)
- [4. ConfigurationClass](5_ConfigurationClassPostProcessor/4_ConfigurationClass.md)
- [5. ConfigurationClassUtils](5_ConfigurationClassPostProcessor/5_ConfigurationClassUtils.md)