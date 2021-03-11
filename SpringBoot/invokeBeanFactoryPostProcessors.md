# invokeBeanFactoryPostProcessors


这一步骤主要是初始化beanFactoryRegistry,并执行一些前置的方法

这里有两种PostProcessor，spring会对这两种分开进行处理
```java
@FunctionalInterface
public interface BeanFactoryPostProcessor {

	/**
	 * Modify the application context's internal bean factory after its standard
	 * initialization. All bean definitions will have been loaded, but no beans
	 * will have been instantiated yet. This allows for overriding or adding
	 * properties even to eager-initializing beans.
	 * @param beanFactory the bean factory used by the application context
	 * @throws org.springframework.beans.BeansException in case of errors
	 */
	void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException;

}
```
```java
public interface BeanDefinitionRegistryPostProcessor extends BeanFactoryPostProcessor {

	/**
	 * Modify the application context's internal bean definition registry after its
	 * standard initialization. All regular bean definitions will have been loaded,
	 * but no beans will have been instantiated yet. This allows for adding further
	 * bean definitions before the next post-processing phase kicks in.
	 * @param registry the bean definition registry used by the application context
	 * @throws org.springframework.beans.BeansException in case of errors
	 */
	void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException;

}
```

## 具体步骤

+ 首先会将一些内置的PostProcessor根据是否为BeanDefinitionRegistryPostProcessor或者BeanFactoryPostProcessor分为两个部分，registryProcessors和regularPostProcessors，并对registryProcessor.postProcessBeanDefinitionRegistry()执行equals方法,
***这里面的内置postProcessor很重要，例如ConfigurationClassPostProcessor***

+ 经过上一步执行的方法，执行实现了BeanDefinitionRegistryPostProcessor和PriorityOrdered接口的方法

+ 执行实现了BeanDefinitionRegistryPostProcessor和Ordered接口的方法

+ 执行实现了BeanDefinitionRegistryPostProcessor接口的方法

+ 之后同样的执行BeanFactoryPostProcessor PriorityOrdered 、 Ordered 普通的方法