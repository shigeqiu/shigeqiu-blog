# Create Bean 

抽象方法

``` java

public abstract class AbstractBeanFactory extends FactoryBeanRegistrySupport implements ConfigurableBeanFactory {

    protected abstract Object createBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
			throws BeanCreationException;

}
```

实现方法

Central method of this class: creates a bean instance, populates the bean instance, applies post-processors, etc. AbstractAutowireCapableBeanFactory 类的中心方法：创建bean实例、填充bean实例、应用后置处理器等
``` java

public abstract class AbstractAutowireCapableBeanFactory extends AbstractBeanFactory
		implements AutowireCapableBeanFactory {

    @Override
	protected Object createBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
			throws BeanCreationException {
    }
    
}
```

## 方法体

### 1

``` java
// Make sure bean class is actually resolved at this point, and
// clone the bean definition in case of a dynamically resolved Class
// which cannot be stored in the shared merged bean definition.
Class<?> resolvedClass = resolveBeanClass(mbd, beanName);
if (resolvedClass != null && !mbd.hasBeanClass() && mbd.getBeanClassName() != null) {
	mbdToUse = new RootBeanDefinition(mbd);
	mbdToUse.setBeanClass(resolvedClass);
}
```

Make sure bean class is actually resolved at this point, and
 clone the bean definition in case of a dynamically resolved Class
 which cannot be stored in the shared merged bean definition.

确保此时 bean class 已经被解析，并且在动态解析类的情况下克隆 bean definition 不能存储在共享合并bean定义中。

### 2

Prepare method overrides.准备方法重写。


### 3

Give BeanPostProcessors a chance to return a proxy instead of the target bean instance. 
让beanPostProcessors有机会返回代理而不是目标bean实例。

### 4

doCreateBean

