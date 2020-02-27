# ConfigurationClassBeanDefinitionReader

类的常量，修饰符均为 **`private static final`** 如下 **↓↓↓**

| 类型  | 变量名 | 默认值
---|---|---
ScopeMetadataResolver | scopeMetadataResolver | `new AnnotationScopeMetadataResolver()`

类的成员变量,修饰符均为 **`private final`** 如下 **↓↓↓**

 | 类型  | 变量名 | 默认值
|---|---|---
| `BeanDefinitionRegistry` | registry | 初始化赋值
| `SourceExtractor` | sourceExtractor | 初始化赋值
| `ResourceLoader` | resourceLoader | 初始化赋值
| `Environment` | environment | 初始化赋值
| `BeanNameGenerator` | importBeanNameGenerator | 初始化赋值
| `ImportRegistry` | importRegistry | 初始化赋值
| `ConditionEvaluator` | conditionEvaluator | 初始化赋值