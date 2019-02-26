# Aware

## 是什么

IOC 容器的 bean 实现了此接口，可以把容器中的组件注入给Bean，

```java
public interface Aware {

}
```

因为这个接口什么方法都没有，只是起一个标识作业，所以真正有实际意义的是其子类接口，如`ApplicationContextAware`,

```java
public interface ApplicationContextAware extends Aware {

	void setApplicationContext(ApplicationContext applicationContext) throws BeansException;

}
```

以`ApplicationContextAware` 为例来看看如何实现向Bean 注入组件的功能

## ApplicationContextAware

//TODO

## 几个特殊的Aware子接口

先看看`AbstractAutowireCapableBeanFactory#initializeBean`

```java
	protected Object initializeBean(final String beanName, final Object bean, @Nullable RootBeanDefinition mbd) {
		if (System.getSecurityManager() != null) {
			AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
				invokeAwareMethods(beanName, bean);
				return null;
			}, getAccessControlContext());
		}
		else {
            //这儿会处理几个Aware 接口的调用
			invokeAwareMethods(beanName, bean);
		}

		Object wrappedBean = bean;
		if (mbd == null || !mbd.isSynthetic()) {
			wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
		}

		try {
			invokeInitMethods(beanName, wrappedBean, mbd);
		}
		catch (Throwable ex) {
			throw new BeanCreationException(
					(mbd != null ? mbd.getResourceDescription() : null),
					beanName, "Invocation of init method failed", ex);
		}
		if (mbd == null || !mbd.isSynthetic()) {
			wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
		}

		return wrappedBean;
	}
```

点进`invokeAwareMethods` 方法看看，是哪几个特殊的Aware 接口

```java
//AbstractAutowireCapableBeanFactory
private void invokeAwareMethods(final String beanName, final Object bean) {
		if (bean instanceof Aware) {
			if (bean instanceof BeanNameAware) {
				((BeanNameAware) bean).setBeanName(beanName);
			}
			if (bean instanceof BeanClassLoaderAware) {
				ClassLoader bcl = getBeanClassLoader();
				if (bcl != null) {
					((BeanClassLoaderAware) bean).setBeanClassLoader(bcl);
				}
			}
			if (bean instanceof BeanFactoryAware) {
				((BeanFactoryAware) bean).setBeanFactory(AbstractAutowireCapableBeanFactory.this);
			}
		}
	}
```

有上面的代码可以看出分别是 **BeanNameAware** ，**BeanClassLoaderAware** ，**BeanFactoryAware**



# BeanDefinition

## 是什么

1. 一个Bean 对应一个 `BeanDefinition`
2. Spring 也就是将用户定义的 Bean 表示成 IoC 容器的内部数据结构：BeanDefinition
3. 是我们的Bean 配置 和 Bean实例之间的中间产品

`org.springframework.beans.factory.config.BeanDefinition` ，是一个接口，它描述了一个 Bean 实例的**定义**，包括属性值、构造方法值和继承自它的类的更多信息。代码如下：



我们常用的三个实现类有：

- `ChildBeanDefinition`
- `RootBeanDefinition`
- `GenericBeanDefinition`
- ChildBeanDefinition、RootBeanDefinition、GenericBeanDefinition 三者都继承 AbstractBeanDefinition 抽象类，即 AbstractBeanDefinition 对三个子类的共同的类信息进行抽象。
- 如果配置文件中定义了父 `<bean>` 和 子 `<bean>` ，则父 `<bean>` 用 RootBeanDefinition 表示，子 `<bean>` 用 ChildBeanDefinition 表示，而没有父 `<bean>` 的就使用RootBeanDefinition 表示。
- GenericBeanDefinition 为一站式服务类。😈 这个解释一脸懵逼？没事，继续往下看。

## 存在哪儿



## 在哪儿创建

在类`BeanDefinitionParserDelegate` 中的 `createBeanDefinition` 方法,看看这儿方法

``

# BeanDefinitionRegistry

实现`BeanDefinition `的注册功能

## 是什么

是一个接口，只要是Bean工厂 或者 ApplicationContext 都会间接或者直接 实现`BeanDefinitionRegistry` 接口



```java

public interface BeanDefinitionRegistry extends AliasRegistry {

	void registerBeanDefinition(String beanName, BeanDefinition beanDefinition)
			throws BeanDefinitionStoreException;
	void removeBeanDefinition(String beanName) throws NoSuchBeanDefinitionException;
	BeanDefinition getBeanDefinition(String beanName) throws NoSuchBeanDefinitionException;
	boolean containsBeanDefinition(String beanName);
	String[] getBeanDefinitionNames();
	int getBeanDefinitionCount();
	boolean isBeanNameInUse(String beanName);
}

```

##举个例子

`AnnotationConfigApplicationContext` 继承于`GenericApplicationContext` ,而我们就可以从`GenericApplicationContext` 类中来看到`BeanDefinitionRegistry` 接口的一些实现方法

```java
//GenericApplicationContext
	private final DefaultListableBeanFactory beanFactory;

	@Override
	public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition)
			throws BeanDefinitionStoreException {

		this.beanFactory.registerBeanDefinition(beanName, beanDefinition);
	}
```



我们从上面的代码可以看出，ApplicationContext的 `BeanFactory` 是委托给 `DefaultListableBeanFactory` 的，所以，我们进入``DefaultListableBeanFactory` ` 的相关方法看看：

```java
//DefaultListableBeanFactory
	private final Map<String, BeanDefinition> beanDefinitionMap = new ConcurrentHashMap<>(256);
	public void registerBeanDefinition(String beanName, BeanDefinition beanDefinition){
		//核心就这一行
		this.beanDefinitionMap.put(beanName, beanDefinition);
	}

	public void removeBeanDefinition(String beanName) throws NoSuchBeanDefinitionException {
		//也是核心就这一行
		BeanDefinition bd = this.beanDefinitionMap.remove(beanName);
	}
```

其实主要就是操作存放 beanDefinition 的 Map



# BeanPostProcessor 

## 是什么

用来在Bean创建好的时候，对Bean做额外的操作

```java
package org.springframework.beans.factory.config;

import org.springframework.beans.BeansException;
import org.springframework.lang.Nullable;

public interface BeanPostProcessor {

	@Nullable
	default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}

	@Nullable
	default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
		return bean;
	}
}

```

## 哪儿被调用

在类`AbstractAutowireCapableBeanFactory`中

```java
	protected Object initializeBean(final String beanName, final Object bean, @Nullable RootBeanDefinition mbd) {
		if (System.getSecurityManager() != null) {
			AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
				invokeAwareMethods(beanName, bean);
				return null;
			}, getAccessControlContext());
		}
		else {
			invokeAwareMethods(beanName, bean);
		}

		Object wrappedBean = bean;
		if (mbd == null || !mbd.isSynthetic()) {
            //即是这儿被调用 Before 方法
			wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
		}

		try {
			invokeInitMethods(beanName, wrappedBean, mbd);
		}
		catch (Throwable ex) {
			throw new BeanCreationException(
					(mbd != null ? mbd.getResourceDescription() : null),
					beanName, "Invocation of init method failed", ex);
		}
		if (mbd == null || !mbd.isSynthetic()) {
            //即是这儿被调用 Before 方法
			wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
		}

		return wrappedBean;
	}
```

我们随便打开`applyBeanPostProcessorsBeforeInitialization` 还是 `applyBeanPostProcessorsAfterInitialization` 方法，都是类似如下的结构，以`applyBeanPostProcessorsBeforeInitialization` 方法为例

```java
//AbstractAutowireCapableBeanFactory#applyBeanPostProcessorsBeforeInitialization
	public Object applyBeanPostProcessorsBeforeInitialization(Object existingBean, String beanName)
			throws BeansException {

		Object result = existingBean;
        //生成的Bean， 要走所有的BeanPostProcessor
		for (BeanPostProcessor beanProcessor : getBeanPostProcessors()) {
			Object current = beanProcessor.postProcessBeforeInitialization(result, beanName);
			if (current == null) {
				return result;
			}
			result = current;
		}
		return result;
	}
```



# InitializingBean（初始化Bean）

## 是什么

由这个接口唯一的一个方法的名字可以看出，大概实在Bean创建好，并且 属性设值之后被调用

```java
public interface InitializingBean {

	void afterPropertiesSet() throws Exception;

}
```



## 哪儿被调用

还是进入Bean的初始化方法：

`AbstractAutowireCapableBeanFactory#initializeBean`

```java
	protected Object initializeBean(final String beanName, final Object bean, @Nullable RootBeanDefinition mbd) {
		if (System.getSecurityManager() != null) {
			AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
				invokeAwareMethods(beanName, bean);
				return null;
			}, getAccessControlContext());
		}
		else {
			invokeAwareMethods(beanName, bean);
		}

		Object wrappedBean = bean;
		if (mbd == null || !mbd.isSynthetic()) {
			wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
		}

		try {
            //看这儿就是调用初始化的方法的意思
			invokeInitMethods(beanName, wrappedBean, mbd);
		}
		catch (Throwable ex) {
			throw new BeanCreationException(
					(mbd != null ? mbd.getResourceDescription() : null),
					beanName, "Invocation of init method failed", ex);
		}
		if (mbd == null || !mbd.isSynthetic()) {
			wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
		}

		return wrappedBean;
	}
```

点进这个方法看看：

```java
//AbstractAutowireCapableBeanFactory
	protected void invokeInitMethods(String beanName, final Object bean, @Nullable RootBeanDefinition mbd)
			throws Throwable {

		boolean isInitializingBean = (bean instanceof InitializingBean);
		if (isInitializingBean && (mbd == null || !mbd.isExternallyManagedInitMethod("afterPropertiesSet"))) {
			if (logger.isDebugEnabled()) {
				logger.debug("Invoking afterPropertiesSet() on bean with name '" + beanName + "'");
			}
			if (System.getSecurityManager() != null) {
				try {
					AccessController.doPrivileged((PrivilegedExceptionAction<Object>) () -> {
						((InitializingBean) bean).afterPropertiesSet();
						return null;
					}, getAccessControlContext());
				}
				catch (PrivilegedActionException pae) {
					throw pae.getException();
				}
			}
			else {
                //一，这儿就是 afterPropertiesSet 被调用的地方
				((InitializingBean) bean).afterPropertiesSet();
			}
		}

		if (mbd != null && bean.getClass() != NullBean.class) {
			String initMethodName = mbd.getInitMethodName();
			if (StringUtils.hasLength(initMethodName) &&
					!(isInitializingBean && "afterPropertiesSet".equals(initMethodName)) &&
					!mbd.isExternallyManagedInitMethod(initMethodName)) {
                //二，这儿就是调用自定义的初始化方法
				invokeCustomInitMethod(beanName, bean, mbd);
			}
		}
	}
```

看到这儿我们已经知道接口`InitializingBean `的 `afterPropertiesSet` 在哪儿调用的了，刚刚我们看到了自定义的初始化方法也会在这儿被调用，那么我们不妨也看看 `invokeCustomInitMethod`：

##自定义初始化方法调用 invokeCustomInitMethod 

```java
//AbstractAutowireCapableBeanFactory
	protected void invokeCustomInitMethod(String beanName, final Object bean, RootBeanDefinition mbd)
			throws Throwable {
      //删除了很多的安全检查代码，
      String initMethodName = mbd.getInitMethodName();
      ReflectionUtils.makeAccessible(initMethod);
      initMethod.invoke(bean);
	}
```

由上面的代码可以看出两点；

1. 自定义的初始化方法是被框架使用反射调用的
2. 自定义的初始化方法是存储在 `BeanDefinition` 的 `initMethodName` 属性中











