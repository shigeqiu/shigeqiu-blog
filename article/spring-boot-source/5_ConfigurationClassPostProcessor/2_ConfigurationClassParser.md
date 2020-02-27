# ConfigurationClassParser

<!-- TOC -->

- [ConfigurationClassParser](#configurationclassparser)
	- [成员](#%E6%88%90%E5%91%98)
	- [processConfigurationClass方法](#processconfigurationclass%E6%96%B9%E6%B3%95)
	- [doProcessConfigurationClass 方法](#doprocessconfigurationclass-%E6%96%B9%E6%B3%95)

<!-- /TOC -->

## 成员

类的常量 **↓↓↓**

``` java
private static final PropertySourceFactory DEFAULT_PROPERTY_SOURCE_FACTORY = new DefaultPropertySourceFactory();

private static final Comparator<DeferredImportSelectorHolder> DEFERRED_IMPORT_COMPARATOR =
		(o1, o2) -> AnnotationAwareOrderComparator.INSTANCE.compare(o1.getImportSelector(), o2.getImportSelector());
```

类的成员变量 **↓↓↓**

| 修饰符 | 类型  | 变量名 | 默认值
---|---|---|---
| private final | `MetadataReaderFactory` | metadataReaderFactory | 初始化赋值
| private final | `ProblemReporter` | problemReporter | 初始化赋值
| private final | `Environment` | environment | 初始化赋值
| private final | `ResourceLoader` | resourceLoader | 初始化赋值
| private final | `BeanDefinitionRegistry` | registry | 初始化赋值
| private final | `ComponentScanAnnotationParser` | componentScanParser | 初始化赋值
| private final | `ConditionEvaluator` | conditionEvaluator | 初始化赋值
| private final | `Map<ConfigurationClass, ConfigurationClass>` | configurationClasses | `new LinkedHashMap<>()`
| private final | `Map<String, ConfigurationClass>` | knownSuperclasses | `new HashMap<>()`
| private final | `List<String>` | propertySourceNames | `new ArrayList<>()`
| private final | `ImportStack` | importStack | `new ImportStack()`
| private  | `List<DeferredImportSelectorHolder>` | deferredImportSelectors | 


## processConfigurationClass方法

参数

| 类型  | 变量  |
|---|---|
| ConfigurationClass  | configClass  |

首先根据`@conditional`注释贩判断是否应跳过该`configClass`。

通过`configClass`获取`configurationClasses`中的对象`existingClass`，

1. 如果`configClass.isImported()`并且`existingClass.isImported()`为true则合并。
2. 如果仅仅`configClass.isImported()`为true，则忽略新的`configClass`,现存的`existingClass`优先于它，返回。
3. 如果`configClass.isImported()`为false，找到明确的bean definition，可能会替换一个import，我们把这个旧的拆下来，然后去配置一个新的。


最后递归处理配置类及其超类层次结构。

## doProcessConfigurationClass 方法

- 注释：Recursively process any member (nested) classes first
- 翻译：首先递归处理任何成员（嵌套）类

``` java
processMemberClasses(configClass, sourceClass);
```

- 注释：Process any @PropertySource annotations
- 翻译：处理任何@PropertySource注释

```
for (AnnotationAttributes propertySource : AnnotationConfigUtils.attributesForRepeatable(
		sourceClass.getMetadata(), PropertySources.class,
		org.springframework.context.annotation.PropertySource.class)) {
	if (this.environment instanceof ConfigurableEnvironment) {
		processPropertySource(propertySource);
	}
	else {
		logger.warn("Ignoring @PropertySource annotation on [" + sourceClass.getMetadata().getClassName() +
				"]. Reason: Environment must implement ConfigurableEnvironment");
	}
}
```