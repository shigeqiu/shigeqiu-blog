# SourceClass

该类实现了`Ordered`接口，内部成员仅有两个，分别为：
- Object source
- AnnotationMetadata metadata

虽然source声明的为Object类型，其实仅支持两种类型，`Class`和`MetadataReader`。metadata成员根据source的类型不同，赋的值也不同，如果source为`Class`类型，则metadata是new一个`StandardAnnotationMetadata`类型的变量。如果为`MetadataReader`类型，则直接调用`getAnnotationMetadata()`方法即可。