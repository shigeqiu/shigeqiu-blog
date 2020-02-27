# 初始化与销毁Bean

- 第一种：通过`@PostConstruct` 和 `@PreDestroy` **在方法注解，**  实现初始化和销毁bean之前进行的操作

- 第二种是：通过在xml中定义`init-method` 和  `destory-method`方法，**或者在@Bean注解上添加该属性。**

- 第三种是： 通过bean实现`InitializingBean`和 `DisposableBean`接口
