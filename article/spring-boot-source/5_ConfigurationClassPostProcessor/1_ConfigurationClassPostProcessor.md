# ConfigurationClassPostProcessor

<!-- TOC -->

- [ConfigurationClassPostProcessor](#configurationclasspostprocessor)
  - [内置对象](#%E5%86%85%E7%BD%AE%E5%AF%B9%E8%B1%A1)
  - [对比reader和parser](#%E5%AF%B9%E6%AF%94reader%E5%92%8Cparser)
  - [processConfigBeanDefinitions方法](#processconfigbeandefinitions%E6%96%B9%E6%B3%95)

<!-- /TOC -->

![ae](../img/ConfigurationClassPostProcessor.png)

上图为**实现类ConfigurationClassPostProcessor**的继承关系

## 内置对象

类的成员变量可见性均为**private** **↓↓↓**

| 修饰符 | 类型  | 变量名 | 默认值
---|---|---|---
|| `SourceExtractor` | sourceExtractor | new PassThroughSourceExtractor()
|| `ProblemReporter` | problemReporter | new FailFastProblemReporter()
|| `Environment` | environment | 
|| `ResourceLoader` |  resourceLoader | new DefaultResourceLoader()
|| `ClassLoader` |  beanClassLoader | ClassUtils.getDefaultClassLoader()
|| `MetadataReaderFactory` |  metadataReaderFactory | new CachingMetadataReaderFactory()
|| `boolean` |  setMetadataReaderFactoryCalled | false
|final | `Set<Integer>` |  registriesPostProcessed  | `new HashSet<>()`
|final | `Set<Integer>` |  factoriesPostProcessed | `new HashSet<>()`
|| `ConfigurationClassBeanDefinitionReader` |  reader | 
|| `boolean` |  localBeanNameGeneratorSet| false
|| `BeanNameGenerator` |  componentScanBeanNameGenerator | new AnnotationBeanNameGenerator()
|| `BeanNameGenerator` |  importBeanNameGenerator | new AnnotationBeanNameGenerator()

## 对比reader和parser

| 类型  | 变量名 |ConfigurationClassPostProcessor| reader | parser
---|---|---|---|---
| SourceExtractor | sourceExtractor |√|√|
| ProblemReporter | problemReporter |√| |√
| Environment | environment |√| √|√
| ResourceLoader |  resourceLoader |√|√|√
| ClassLoader |  beanClassLoader |√| |
| MetadataReaderFactory |  metadataReaderFactory |√| |√
| ConfigurationClassBeanDefinitionReader |  reader |√| |
| BeanNameGenerator |  componentScanBeanNameGenerator |√| |
| BeanNameGenerator |  importBeanNameGenerator |√|√|
| ConditionEvaluator |  conditionEvaluator ||√|√|
|importStack|importStack|||√|
|importRegistry|importRegistry||√||
|BeanDefinitionRegistry|registry||√|√|
|ComponentScanAnnotationParser|componentScanParser|||√


## processConfigBeanDefinitions方法

参数：

| type  |  value | 
|---|---|
| BeanDefinitionRegistry  | registry  |

``` java
//Candidate 候选人
List<BeanDefinitionHolder> configCandidates = new ArrayList<>();
String[] candidateNames = registry.getBeanDefinitionNames();
```

循环`candidateNames`数组变量为beanName，通过registry获取`BeanDefinition`类型的对象并赋值给变量`beanDef`，然后进行比对属性

1. 满足`ConfigurationClassUtils`的方法`isFullConfigurationClass(beanDef)`或者方法`isLiteConfigurationClass(beanDef)`其中之一，则不处理，打印一行debug日志。
2. 满足`ConfigurationClassUtils`的方法`checkConfigurationClassCandidate()`，则创建`BeanDefinitionHolder`实例，将BeanDefinition和beanName作为初始化参数传入，所创建的`BeanDefinitionHolder`实例添加到变量`configCandidates`集合中。

如果configCandidates集合为空则返回不处理。
- 注释：Return immediately if no @Configuration classes were found
- 翻译：如果没有找到@Configuration类，则立即返回。
  
调用configCandidates集合的sort()方法进行排序。
- 注释：Sort by previously determined @Order value, if applicable
- 翻译：按先前确定的@order value排序（如果适用）

构造`ConfigurationClassParser`类型的变量parser。

``` java
Set<BeanDefinitionHolder> candidates = new LinkedHashSet<>(configCandidates);
Set<ConfigurationClass> alreadyParsed = new HashSet<>(configCandidates.size());
```

然后进入循环体,直到`candidates`集合变量为空。

循环体内执行开始：

```
parser.parse(candidates);
parser.validate();
```
Set<ConfigurationClass> configClasses = new LinkedHashSet<>(parser.getConfigurationClasses());


``` java
Set<ConfigurationClass> configClasses = new LinkedHashSet<>(parser.getConfigurationClasses());
configClasses.removeAll(alreadyParsed);
```
创建configClasses集合变量，通过`parser.getConfigurationClasses()`方法的返回值初始化该集合变量。

获取`ConfigurationClassBeanDefinitionReader`类型的变量reader，如果reader为空则初始化构造，若不为空，则不必初始化。

``` java
this.reader.loadBeanDefinitions(configClasses);
alreadyParsed.addAll(configClasses);

candidates.clear();
```

循环体结束。