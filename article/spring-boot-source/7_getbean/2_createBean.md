# Create Bean 

<!-- TOC -->

- [Create Bean](#create-bean)
	- [位置](#%e4%bd%8d%e7%bd%ae)
	- [方法体](#%e6%96%b9%e6%b3%95%e4%bd%93)
		- [deep copy](#deep-copy)
		- [prepare overrides](#prepare-overrides)
		- [shortcut](#shortcut)
		- [doCreateBean](#docreatebean)

<!-- /TOC -->

## 位置

抽象方法

``` java

public abstract class AbstractBeanFactory extends FactoryBeanRegistrySupport implements ConfigurableBeanFactory {

    protected abstract Object createBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args)
			throws BeanCreationException;

}
```

实现方法如下：

Central method of this class: creates a bean instance, populates the bean instance, applies post-processors, etc. AbstractAutowireCapableBeanFactory   
类的中心方法：创建bean实例、填充bean实例、应用后置处理器等
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

### deep copy

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

### prepare overrides

``` java
// Prepare method overrides.
try {
	mbdToUse.prepareMethodOverrides();
}
catch (BeanDefinitionValidationException ex) {
	throw new BeanDefinitionStoreException(mbdToUse.getResourceDescription(),
			beanName, "Validation of method overrides failed", ex);
}
```

Prepare method overrides.准备方法重写。


### shortcut

``` java
try {
	// Give BeanPostProcessors a chance to return a proxy instead of the target bean instance.
	Object bean = resolveBeforeInstantiation(beanName, mbdToUse);
	if (bean != null) {
		return bean;
	}
}
catch (Throwable ex) {
	throw new BeanCreationException(mbdToUse.getResourceDescription(), beanName,
			"BeanPostProcessor before instantiation of bean failed", ex);
}
```

Give BeanPostProcessors a chance to return a proxy instead of the target bean instance.   
让beanPostProcessors有机会返回代理而不是目标bean实例。

Apply before-instantiation post-processors, resolving whether there is a before-instantiation shortcut for the specified bean.  
在实例化后处理器之前应用，解析指定bean是否存在实例化前快捷方式。

### doCreateBean

``` java
try {
	Object beanInstance = doCreateBean(beanName, mbdToUse, args);
	if (logger.isTraceEnabled()) {
		logger.trace("Finished creating instance of bean '" + beanName + "'");
	}
	return beanInstance;
}
catch (BeanCreationException | ImplicitlyAppearedSingletonException ex) {
	// A previously detected exception with proper bean creation context already,
	// or illegal singleton state to be communicated up to DefaultSingletonBeanRegistry.
	throw ex;
}
catch (Throwable ex) {
	throw new BeanCreationException(
			mbdToUse.getResourceDescription(), beanName, "Unexpected exception during bean creation", ex);
}
```
