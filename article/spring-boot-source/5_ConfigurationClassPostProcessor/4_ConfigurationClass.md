# ConfigurationClass


 类型  | 变量名 | 默认值
---|---|---
 `AnnotationMetadata` | metadata | 初始化赋值
 `Resource` | resource | 初始化赋值
 `String` | beanName | 初始化赋值
 `Set<ConfigurationClass>` | importedBy | `new LinkedHashSet<>(1)`
 `Set<BeanMethod>` | beanMethods | `new LinkedHashSet<>()`
 `Map<String, Class<? extends BeanDefinitionReader>>` | importedResources | `new LinkedHashMap<>()`
 `Map<ImportBeanDefinitionRegistrar, AnnotationMetadata>` | importBeanDefinitionRegistrars | `new LinkedHashMap<>()`
 `Set<String>` | skippedBeanMethods | `new HashSet<>()`


 ## 初始化

 ```
public ConfigurationClass(MetadataReader metadataReader, String beanName) {
		Assert.notNull(beanName, "Bean name must not be null");
		this.metadata = metadataReader.getAnnotationMetadata();
		this.resource = metadataReader.getResource();
		this.beanName = beanName;
	}
 ```

 ```
public ConfigurationClass(MetadataReader metadataReader, @Nullable ConfigurationClass importedBy) {
		this.metadata = metadataReader.getAnnotationMetadata();
		this.resource = metadataReader.getResource();
		this.importedBy.add(importedBy);
	}
 ```