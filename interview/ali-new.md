[TOC]



# 归纳总结

1.项目中学到了什么技术

2.微服务的划分颗粒

3.微服务的高可用怎么保证？

4.常用的负载均衡，该怎么用？你能说下吗？

5.网关能够为后端服务带来哪些好处

10.hashcode和equals方法常用的地方

13.object toString方法常用的地方，为什么要重写该方法

14.set和list的区别

17.set村的顺序是有序的吗？

18.常见set的实现由哪些

19.TreeSet对存入对数据有什么要求

20.hashset的底层实现呢

21.treeset底层源码有看过吗？

22.hashset是不是线程安全的，为什么不是线程安全的

23.java中有哪些线程安全的map

26.如何保证线程安全问题？

27.synchronized，lock

28.volatile的原子性问题？为什么 i++不支持原子性，从计算机原理的设计来讲下不能保证原子性的原因

30.cas操作

31.lock和synchronized的区别

32.公平[锁和](https://www.baidu.com/s?wd=%E9%94%81%E5%92%8C&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)非公平锁

33.java[读写锁](https://www.baidu.com/s?wd=%E8%AF%BB%E5%86%99%E9%94%81&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)

34.读写锁设计主要解决什么问题

35.你项目除了写java代码，还有前段代码，那你知道前段有哪些框架吗？

36.mysql分页查询语句

40.前段游览器的一个http请求道后端整个流程是怎么样的？能够说下吗？

42.dns你知道是干嘛的吗？

44.代码版本管理用什么

45.git rebase和merge有什么区别



1.项目中的监控：那个监控指标常见有哪些

2.为服务设计到的技术以及需要注意的问题有哪些

3.注册中心你了解了解哪些

4.consul的可靠性你了解吗

5.consul的机制你有具体深入了解过吗？有没有和其他的注册中心对比过

7.Spring boot除了自动装配，相比传统的Spring有哪些其他的区别

8.Spring cloud有了解多少

13.线上服务[CPU](https://www.baidu.com/s?wd=CPU&tn=24004469_oem_dg&rsv_dl=gh_pl_sl_csd)很高该怎么做？有哪些措施可以找到问题？

19.说下你知道的排序算法

## Java基础

### 反射

#### 反射的原理



#### 反射创建类实例的三种方式是什么

1. 调用类的Class对象的newInstance方法，该方法会调用对象的默认构造器，如果没有默认构造器，会调用失败.
2. 调用默认Constructor对象的newInstance方法
3. 调用带参数Constructor对象的newInstance方法

### 设计模式

#### 单例

##### 写出三种单例模式实现 

**懒汉**，线程安全

```java
public class Singleton {
    private static Singleton instance;
    private Singleton (){}

    public static synchronized Singleton getInstance() {
	if (instance == null) {
	    instance = new Singleton();
	}
	return instance;
    }
}
```

这种写法能够在多线程中很好的工作，而且看起来它也具备很好的lazy loading，但是，遗憾的是，效率很低，99%情况下不需要同步。

饿汉

```java
public class Singleton {  
    private static Singleton instance = new Singleton();  
    private Singleton (){}  
    public static Singleton getInstance() {  
    return instance;  
    }  
}
```

静态内部类

```java
public class Singleton {  
    private static class SingletonHolder {  
    private static final Singleton INSTANCE = new Singleton();  
    }  
    private Singleton (){}  
    public static final Singleton getInstance() {  
    return SingletonHolder.INSTANCE;  
    }  
} 
```

枚举 

```java
public enum Singleton {
    INSTANCE;
    public void whateverMethod() {
    }
}
```



双重校验锁

```java
public class Singleton {
    private volatile static Singleton singleton;
    private Singleton (){}
    public static Singleton getSingleton() {
	if (singleton == null) {
	    synchronized (Singleton.class) {
		if (singleton == null) {
		    singleton = new Singleton();
		}
	    }
	}
	return singleton;
    }
}
```

注意一定要加上`volatile` 关键字

### 泛型

#### 泛型的存在是用来解决什么问题

类型安全，主要是向下转型

消除强制类型转换

### 异常

#### 请列出5个运行时异常

### Socket

#### Socket 工作在 TCP/IP 协议栈是哪一层

socket的实现部分, 就是系统协议栈部分， 应该包含了 网络层 (ip), 传输层(tcp/udp)等等

#### socket 选项 TCP NO DELAY 是指什么

#### TCP、UDP 区别及 Java 实现方式

#### 说几点 IO 的最佳实践

#### 直接缓冲区与非直接缓冲器有什么区别？

#### 怎么读写 ByteBuffer？ByteBuffer 中的字节序是什么

#### 当用System.in.read(buffer)从键盘输入一行n个字符后，存储在缓冲区buffer中的字节数是多少

#### 如何使用扫描器类（Scanner Class）令牌化

### 集合

#### 熟悉的集合有哪些，讲讲设计比较好的地方

1. HashMap 使用 `hash & (n - 1)` 来取模

2. ConcurrentHash 在 1.8 扩容(`transfer`)的时候，在计算新桶位的时候没有使用以前的老方式，而是采用一种取巧的方式 `(h & n) == 0` 则是在原来的位置上，否则，就是在相应位置的高位





##Project

### 项目中使用的设计模式

模板方法、

单例模式、

建造者模式：Protobuf ,生成的协议对象在构建的时候

责任链模式：Netty 的 ChannelPipeline 基于责任链模式开发

### 你觉得项目里给里最大的挑战是什么？ 



## Framework

###Spring

**项目用Spring比较多，有没有了解过Spring的原理？AOP和IOC的原理**

       AOP（这里的AOP指的是面向切面编程思想，而不是Spring AOP）主要的的实现技术主要有Spring AOP和AspectJ。

1、AspectJ的底层技术。

       AspectJ的底层技术是静态代理，即用一种AspectJ支持的特定语言编写切面，通过一个命令来编译，生成一个新的代理类，该代理类增强了业务类，这是在编译时增强，相对于下面说的运行时增强，编译时增强的性能更好。

2、Spring AOP

       Spring AOP采用的是动态代理，在运行期间对业务方法进行增强，所以不会生成新类，对于动态代理技术，Spring AOP提供了对JDK动态代理的支持以及CGLib的支持。

       JDK动态代理只能为接口创建动态代理实例，而不能对类创建动态代理。需要获得被目标类的接口信息（应用Java的反射技术），生成一个实现了代理接口的动态代理类（字节码），再通过反射机制获得动态代理类的构造函数，利用构造函数生成动态代理类的实例对象，在调用具体方法前调用invokeHandler方法来处理。

       CGLib动态代理需要依赖asm包，把被代理对象类的class文件加载进来，修改其字节码生成子类。

​	但是Spring AOP基于注解配置的情况下，需要依赖于AspectJ包的标准注解，但是不需要额外的编译以及AspectJ的织入器，而基于XML配置不需要。





####Spring bean的生命周期

主要的创建流程都在方法`AbstractAutowireCapableBeanFactory#doCreateBean` 中，下面来一步一步的看

##### 1.实例化 Bean 对象(instantiateBean)

在`Spring` 中 `instantiate` 表示实例化的意思

1. ```
   BeanWrapper instanceWrapper = createBeanInstance(beanName, mbd, args);
   ```

2. 进入`createBeanInstance` 方法中，此方法会调用`instantiateBean(beanName, mbd);` 方法

##### 2.属性注入(populateBean)

调用`populateBean(beanName, mbd, instanceWrapper);` 方法进行属性输入

##### 3.初始化数据(initializeBean)

初始化数据主要使用了如下的方法：

```java
protected Object initializeBean(final String beanName, final Object bean, @Nullable RootBeanDefinition mbd) {
    invokeAwareMethods(beanName, bean);
    wrappedBean = applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
    invokeInitMethods(beanName, wrappedBean, mbd);
	wrappedBean = applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
	return wrappedBean;
}
```

具体的解释看下面：



###### invokeAwareMethods

Aware 接口的注入，对应NameAware，BeanClassLoaderAware，BeanFactoryAware

###### applyBeanPostProcessorsBeforeInitialization

循环调用`BeanPostProcessor `  的  `postProcessBeforeInitialization` 方法

###### invokeInitMethods

此方法用来执行初始化方法，那么初始化方法有哪些呢，参看 Spring 下面的面试题 `指定Bean的初始化和销毁方法`

我们直接来看看代码实现：

```java
protected void invokeInitMethods(String beanName, final Object bean, @Nullable RootBeanDefinition mbd)
		throws Throwable {
	//省略部分代码
    //如果实现了InitializingBean 接口，调用其afterPropertiesSet 方法
	boolean isInitializingBean = (bean instanceof InitializingBean);
	((InitializingBean) bean).afterPropertiesSet();


	if (mbd != null && bean.getClass() != NullBean.class) {
		String initMethodName = mbd.getInitMethodName();
		if (StringUtils.hasLength(initMethodName) &&
				!(isInitializingBean && "afterPropertiesSet".equals(initMethodName)) &&
				!mbd.isExternallyManagedInitMethod(initMethodName)) {
            //调用自定义的 Init 方法，就是我们使用xml配置，或者@Bean注解设置的 init-method 属性指定的方法
			invokeCustomInitMethod(beanName, bean, mbd);
		}
	}
}
```

JSR250 规范中使用`@PostConstruct` 在代码中哪里实现的留待以后查看//TODO

###### applyBeanPostProcessorsAfterInitialization

循环调用`BeanPostProcessor `  的  `postProcessAfterInitialization` 方法

##### 4.Bean 的销毁

如果 Bean 实现 **DisposableBean** 接口，当 spring 容器关闭时，会调用 `#destroy()` 方法。

如果为 bean 指定了 **destroy** 方法（例如 `<bean />` 的 `destroy-method` 属性），那么将调用该方法。



#### 讲讲Spring加载流程

核心在AbstractApplicationContext的refresh方法

实例化 `BeanFactoryPostProcessor` 实现类 ,

然后调用其 `postProcessBeanFactory` 方法

实例化`BeanPostProcessor` 实现类

实例化  InstantiationAwareBeanPostProcessor 实现类，它也是 `extends BeanPostProcessor`

#### Spring AOP的实现原理

#### Spring事务

##### 讲讲Spring事务的传播属性

##### Spring如何管理事务的

####Spring怎么解决循环依赖



#### FactoryBean

1. **为什么有FactoryBean：**在某些情况下，实例化 `bean` 比较复杂，例如有多个参数的情况下，传统方式需要在配置文件中，写很多配置信息，显得不太灵活。在这种情况下，可以使用 `Spring` 提供的 `FactoryBean` 接口，用户可以通过实现该接口定制实例化 `bean` 的逻辑。

2. 当配置文件中的 `<bean>` 的 `class` 属性实现类是 `FactoryBean` 时，通过 `getBean()` 方法返回的不是 `FactoryBean` 本身，而是 `FactoryBean#getObject()` 方法返回的对象。

3. 通过上下文获取类的方法 `context.getBean("beanName")`，使用区别是 `beanName` 是否使用 & 前缀，如果没有 & 前缀，识别到的是 FactoryBean.getObject 返回的类型，如果带上 & 前缀，那么将会返回 `FactoryBean` 类型的类。以`YamlPropertiesFactoryBean` 为例, 如果不带&前缀，返回`Properties`类型，带&前缀返回`YamlPropertiesFactoryBean` 类型

   ```java
   public class YamlPropertiesFactoryBean extends YamlProcessor implements FactoryBean<Properties>, InitializingBean {
       public Properties getObject() {
   		return (this.properties != null ? this.properties : createProperties());
   	}
   }
   ```

4. 

#### BeanFactory 和 FactoryBean的区别？

####XML中配置init，destroy方法怎么可以做到调用具体的方法？



AbstractAutowireCapableBeanFactory 的  initializeBean 方法中，会调用applyBeanPostProcessors 的 before 和 after 方法，然后，这两处调用的中间，有个 `invokeInitMethods` 方法，进入方法，会调用 `InitializingBean` 类的 `afterPropertiesSet` 方法之后，然后会尝试调用 `invokeCustomInitMethod` 



####注解注册bean到ioc容器的几种方式

1. 在上面的配置类上加`@Service`或者`@Component`等注解，springboot会扫描启动类所在的包下面所有带有这些注解的类，实例化bean加到ioc容器。

2. 使用`@Configuration`和`@Bean`注解来配置bean到ioc容器,这个类也需要在springboot启动类所在的包或者子包下面，否则无法扫到。

3. 使用`@Import`注解

   相信很多人对`@EnableScheduling` 、`@EnableCaching`等@Enablexxxx系列的注解都不陌生，它们就是使用的是@Import注解来实现开启xx功能的。

#### 指定Bean的初始化和销毁方法

**1. **通过@Bean指定init-method和destroy-method；

**2. **通过让Bean实现InitializingBean（定义初始化逻辑），DisposableBean（定义销毁逻辑）;

**3.** 可以使用JSR250；

@PostConstruct：在bean创建完成并且属性赋值完成；来执行初始化方法

@PreDestroy：在容器销毁bean之前通知我们进行清理工作

#### 自定义标签

##### 如何自定义标签

1. 默认位置在 `resources` -> `META-INF`下面，新建两个文件 **spring.hanlders 和 spring.schemas**分别放对应的 `handler` 和 `XSD`

   ```properties
   #spring.handlers 文件
   http\://www.springframework.org/schema/aop=org.springframework.aop.config.AopNamespaceHandler

   #spring.schemas 文件
   http\://www.springframework.org/schema/aop/spring-aop-4.0.xsd = org/springframework/aop/config/spring-aop.xsd
   ```


2. 定义`handler`，用来注册`parser`,扩展了 `NamespaceHandlerSupport` 的类，在初始化注册解析器

   ```java
   public class AopNamespaceHandler extends NamespaceHandlerSupport {
   	@Override
   	public void init() {
   		// In 2.0 XSD as well as in 2.1 XSD.
   		registerBeanDefinitionParser("config", new ConfigBeanDefinitionParser());
   		registerBeanDefinitionParser("aspectj-autoproxy", new AspectJAutoProxyBeanDefinitionParser());
   		registerBeanDefinitionDecorator("scoped-proxy", new ScopedProxyBeanDefinitionDecorator());

   		// Only in 2.0 XSD: moved to context namespace as of 2.1
   		registerBeanDefinitionParser("spring-configured", new SpringConfiguredBeanDefinitionParser());
   	}
   }
   ```

3. 定义**运行解析器  Parser**：扩展了 `AbstractSingleBeanDefinitionParser`，通过重载方法进行属性解析，完成解析。以aop的`AspectJAutoProxyBeanDefinitionParser` 解析器为例子

   ```java
   class AspectJAutoProxyBeanDefinitionParser implements BeanDefinitionParser {

   	@Override
   	@Nullable
   	public BeanDefinition parse(Element element, ParserContext parserContext) {
   	AopNamespaceUtils.registerAspectJAnnotationAutoProxyCreatorIfNecessary(parserContext, element);
   		extendBeanDefinition(element, parserContext);
   		return null;
   	}
   }
   ```

4. 

### SpringMVC

#### Springmvc 中DispatcherServlet初始化过程



#### Controller是单例还是多例，怎么保证并发的安全

Controller默认是单例,



### SpringBoot

####SpringBoot starter 原理（自动配置原理）

首先说说原理，我们知道使用一个公用的starter的时候，只需要将相应的依赖添加的Maven的配置文件当中即可，免去了自己需要引用很多依赖类，并且SpringBoot会自动进行类的自动配置。那么 SpringBoot 是如何知道要实例化哪些类，并进行自动配置的呢？ 下面简单说一下。

1. 首先，SpringBoot 在启动时会去依赖的starter包中寻找 `resources/META-INF/spring.factories `文件，然后根据文件中配置的Jar包去扫描项目所依赖的Jar包，这类似于 Java 的 **SPI** 机制。
2. 第二步，根据 `spring.factories`配置加载`AutoConfigure`类。
3. 最后，根据 `@Conditional`注解的条件，进行自动配置并将Bean注入Spring Context 上下文当中。我们也可以使用`@ImportAutoConfiguration({MyServiceAutoConfiguration.class})` 指定自动配置哪些类。

下面具体代码：

```java
@EnableAutoConfiguration
public @interface SpringBootApplication {
}

@Import(AutoConfigurationImportSelector.class)
public @interface EnableAutoConfiguration {
}
```

因为使用`@Import` 注解引入`AutoConfigurationImportSelector` 类，进入这个类的`selectImports`

```java
@Override
public String[] selectImports(AnnotationMetadata annotationMetadata) {
    if (!isEnabled(annotationMetadata)) {
        return NO_IMPORTS;
    }
    AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader
        .loadMetadata(this.beanClassLoader);
    //看这里
    AutoConfigurationEntry autoConfigurationEntry = getAutoConfigurationEntry(
        autoConfigurationMetadata, annotationMetadata);
    return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
}
```

进入`AutoConfigurationImportSelector.getAutoConfigurationEntry`方法：

```java
protected AutoConfigurationEntry getAutoConfigurationEntry(
    AutoConfigurationMetadata autoConfigurationMetadata,
    AnnotationMetadata annotationMetadata) {
    AnnotationAttributes attributes = getAttributes(annotationMetadata);
    //省略了其他的代码，主要是这里
    List<String> configurations = getCandidateConfigurations(annotationMetadata,
                                                             attributes);
}
```

进入`AutoConfigurationImportSelector#getCandidateConfigurations`方法，**该方法会去获取所有自动配置类的名称**。

1. 该方法会扫描jar包路径下的`META-INF/spring.factories` 文件，把扫描到的这些文件内容包装成properties对象。
2. 再从properties中获取到EnableAutoConfiguration.class类（类名）为key 对应的值，并且把他们添加到容器中。
3. 打开一个`spring.factories`文件，看到的非常多的xxxxAutoConfiguration类，这些类都是容器中的一个组件，加入到容器中，用他们做自动配置。

我们看一个自动装配的例子`RedisAutoConfiguration`,这是自定义的一个自动装配的类，其中就是借助`Conditional`相关的注解根据不同的条件，向容器中注入bean

```java
@Configuration
@ConditionalOnProperty(value = "spring.redis.enabled",matchIfMissing = true)
@ConditionalOnClass({ RedisTemplate.class })
@EnableCaching
@Slf4j
public class RedisAutoConfiguration extends CachingConfigurerSupport {

	@Bean
	@ConditionalOnMissingBean(RedisTemplate.class)
	public RedisTemplate<String, Object> redisTemplate(@Autowired RedisConnectionFactory redisConnectionFactory,
			@Autowired RedisSerializer<Object> redisSerializer)
			throws UnknownHostException {
		RedisTemplate<String, Object> template = new RedisTemplate<String, Object>();
		template.setConnectionFactory(redisConnectionFactory);
		template.setHashValueSerializer(redisSerializer);
		template.setValueSerializer(redisSerializer);
		StringRedisSerializer stringRedisSerializer = new StringRedisSerializer(Charset.forName("UTF-8"));
		template.setHashKeySerializer(stringRedisSerializer);
		template.setKeySerializer(stringRedisSerializer);
		log.info("redisTemplate init success");
		return template;
	}

	 @Bean
	 @ConditionalOnMissingBean(value = RedisSerializer.class)
     public RedisSerializer<Object> redisSerializer() {
         return new GenericFastJsonRedisSerializer();
     }
}
```



#### 讲一下Springboot的启动流程



####starter 命名格式

我们日常使用的Spring官方的Starter一般采取`spring-boot-starter-{name} `的命名方式，如 `spring-boot-starter-web `。

而非官方的Starter，官方建议 `artifactId` 命名应遵循`{name}-spring-boot-starter `的格式。 例如：`ysc-spring-boot-starter ` 。



#### 自动配置 V.S 自动装配

- 自动配置：是 Spring Boot 提供的，实现通过 jar 包的依赖，能够自动配置应用程序。例如说：我们引入 `spring-boot-starter-web` 之后，就自动引入了 Spring MVC 相关的 jar 包，从而自动配置 Spring MVC 。
- 自动装配：是 Spring 提供的 IoC 注入方式，比如使用使用`byName`或者`byType`自动装配bean对象



### Dubbo

#### Dubbo 有哪些负载均衡策略？



**Random LoadBalance**

- **随机**，按权重设置随机概率。
- 在一个截面上碰撞的概率高，但调用量越大分布越均匀，而且按概率使用权重后也比较均匀，有利于动态调整提供者权重。



**RoundRobin LoadBalance**

- **轮询**，按公约后的权重设置轮询比率。

- 存在慢的提供者累积请求的问题，比如：第二台机器很慢，但没挂，当请求调到第二台时就卡在那，久而久之，所有请求都卡在调到第二台上。

  > 举个栗子。
  >
  > 跟运维同学申请机器，有的时候，我们运气好，正好公司资源比较充足，刚刚有一批热气腾腾、刚刚做好的一批虚拟机新鲜出炉，配置都比较高。8核+16g，机器，2 台。过了一段时间，我感觉 2 台机器有点不太够，我去找运维同学，哥儿们，你能不能再给我 1 台机器，4核+8G的机器。我还是得要。
  >
  > 这个时候，可以给两台 8核16g 的机器设置权重 4，给剩余 1 台 4核8G 的机器设置权重 2。



**LeastActive LoadBalance**

- **最少活跃调用数**，相同活跃数的随机，活跃数指调用前后计数差。

- 使慢的提供者收到更少请求，因为越慢的提供者的调用前后计数差会越大。

  > 这个就是自动感知一下，如果某个机器性能越差，那么接收的请求越少，越不活跃，此时就会给不活跃的性能差的机器更少的请求。



**ConsistentHash LoadBalance**

- **一致性 Hash**，相同参数的请求总是发到同一提供者。
- 当某一台提供者挂时，原本发往该提供者的请求，基于虚拟节点，平摊到其它提供者，不会引起剧烈变动。



#### Dubbo SPI

##### 源码分析

1. 

#### Dubbo监控平台能够动态改变接口的一些设置,其原理是怎样的?

#### 最小活跃数算法中是如何统计这个活跃数的？

针对每个服务接口的每一个方法，定义了一个`RpcStatus` 对象来保存状态，其中有个字段叫`active` atomicInteger 类型的字段用来表示活跃数，在`ActiveLimitFilter`中每当这个方法被调用开始，active 加1，调用返回，active 减1，所以此值越小，代表当前正在处理得调用就越少，所以请求就发给此Invoker

#### 服务提供者能实现失效踢出是什么原理？

可以从两对网络关联关系来说明

**服务提供者和注册中心之间：**

注册中心是Zookeeper，服务提供者，使用临时节点想Zookeeper注册自己，这样服务提供者能提供服务和服务失效，Zookeeper注册中心是知道的

**服务消费者和注册中心之间：**







1. 先来说说服务提供者的注册（使用zookeeper），在`RegistryProtocol` 的 `export` 方法注册的时候，会向Zookeeper注册临时节点

### Mybatis

####  Mybatis的mapper文件中resultType和resultMap的区别

使用resultType进行输出映射，只有查询出来的列名和pojo中的属性名一致，该列才可以映射成功。

 如果查询出来的列名和pojo的属性名不一致，通过定义一个resultMap对列名和pojo属性名之间作一个映射关系。

### Netty

#### 简单介绍 Netty 的核心组件？

Netty 有如下六个核心组件：

- Bootstrap & ServerBootstrap
- Channel
- ChannelFuture
- EventLoop & EventLoopGroup
- ChannelHandler
- ChannelPipeline



#### 什么是 Reactor 模型？

Reactor 有 3 种模型实现：

1. 单 Reactor 单线程模型
2. 单 Reactor 多线程模型
3. 多 Reactor 多线程模型



#### poll、epoll、select 的区别

#### 什么是零拷贝

维基上是这么描述零拷贝的：零拷贝描述的是CPU不执行拷贝数据从一个存储区域到另一个存储区域的任务，这通常用于通过网络传输一个文件时以减少CPU周期和内存带宽。


##### 零拷贝给我们带来的好处：

- 减少甚至完全避免不必要的CPU拷贝，从而让CPU解脱出来去执行其他的任务
- 减少内存带宽的占用
- 通常零拷贝技术还能够减少用户空间和操作系统内核空间之间的上下文切换

https://www.jianshu.com/p/e76e3580e356

#### Netty应用在哪些中间件和框架中呢？ 

Dubbo、RocketMQ作为底层通信框架、Avro IPC

## Network

### TCP滑动窗口协议

1. TCP的滑动窗口主要有两个作用，一是提供TCP的可靠性，二是提供TCP的流控特性，滑动窗口协议是**传输层进行流控**的一种措施，**接收方通过通告发送方自己的窗口大小**，从而控制发送方的发送速度，

2. TCP是双工的协议，会话的双方都可以同时接收、发送数据。TCP会话的双方都各自维护一个“发送窗口”和一个“接收窗口”。其中各自的“接收窗口”大小取决于应用、系统、硬件的限制（TCP传输速率不能大于应用的数据处理速率）。各自的“发送窗口”则要求取决于对端通告的“接收窗口”，要求相同。

3. 对于TCP会话的发送方，任何时候在其发送缓存内的数据都可以分为4类，“已经发送并得到对端ACK的”，“已经发送但还未收到对端ACK的”，“未发送但对端允许发送的”，“未发送且对端不允许发送”。“已经发送但还未收到对端ACK的”和“未发送但对端允许发送的”这两部分数据称之为发送窗口。

4. 对于TCP的接收方，在某一时刻在它的接收缓存内存在3种。“已接收”，“未接收准备接收”，“未接收并未准备接收”（由于ACK直接由TCP协议栈回复，默认无应用延迟，不存在“已接收未回复ACK”）。其中“未接收准备接收”称之为接收窗口。

5. 对ACK的再认识，ack通常被理解为收到数据后给出的一个确认ACK，ACK包含两个非常重要的信息：
   **一是期望接收到的下一字节的序号n，该n代表接收方已经接收到了前n-1字节数据，此时如果接收方收到第n+1字节数据而不是第n字节数据，****接收方是不会发送序号为n+2的ACK的。**举个例子，假如接收端收到1-1024字节，它会发送一个确认号为1025的ACK,但是接下来收到的是2049-3072，它是不会发送确认号为3072的ACK,而依旧发送1025的ACK。

   **二是当前的窗口大小m，如此发送方在接收到ACK包含的这两个数据后就可以计算出还可以发送多少字节的数据给对方，假定当前发送方已发送到第x字节，则可以发送的字节数就是y=m-(x-n).这就是滑动窗口控制流量的基本原理**


**TCP和UDP的区别，TCP数据传输过程如何做到可靠的**



**http默认端口，https默认端口**

http ： 80

https：443





## 性能优化



## Jdk 源码

###List

####ArralList和LinkedList区别

ArrayList 是实现了基于动态数组的数据结构，因为地址连续，一旦数据存储好了，查询操作效率会比较高（在内存里是连着放的）。

因为地址连续，ArrayList 要移动数据，所以插入和删除操作效率比较低。

LinkedList 基于链表的数据结构，地址是任意的，所以在开辟内存空间的时候不需要等一个连续的地址。对于新增和删除操作 add 和 remove ，LinedList 比较占优势。LinkedList 适用于要头尾操作或插入指定位置的场景。

因为 LinkedList 要移动指针，所以查询操作性能比较低。



####如果存相同数据，ArrayList和LinkedList谁占用空间更大

LinkedList 明显更大，因为每个节点有很多的额外数据，比如向前和向后的索引

###Map

#### HashMap

##### 为什么HashMap的容量是2的幂次？

1. 因为位运算的速度比取模快  
2. 如果要使用为位运算取模那么使用这种方式： return h & (length-1);   
3. 使用这种位运算取模的方式，只有当容量为2的幂次的时候，才能使用所有空间



##### Hashmap为什么不用平衡树？ 

红黑树和AVL树都是**最常用的平衡二叉搜索树**，它们的查找、删除、修改都是O(lgn) time

AVL树和红黑树有几点比较和区别：
 （1）AVL树是更加严格的平衡，因此可以提供更快的查找速度，一般读取查找密集型任务，适用AVL树。
 （2）红黑树更适合于插入修改密集型任务。
 （3）通常，AVL树的旋转比红黑树的旋转更加难以平衡和调试。

**总结**：
 （1）AVL以及红黑树是高度平衡的树数据结构。它们非常相似，真正的区别在于在任何添加/删除操作时完成的旋转操作次数。
 （2）两种实现都缩放为a O(lg N)，其中N是叶子的数量，但实际上AVL树在查找密集型任务上更快：利用更好的平衡，树遍历平均更短。另一方面，插入和删除方面，AVL树速度较慢：需要更高的旋转次数才能在修改时正确地重新平衡数据结构。
 （3）在AVL树中，从根到任何叶子的最短路径和最长路径之间的差异最多为1。在红黑树中，差异可以是2倍。
 （4）两个都给O（log n）查找，但平衡AVL树可能需要O（log n）旋转，而红黑树将需要最多两次旋转使其达到平衡（尽管可能需要检查O（log n）节点以确定旋转的位置）。旋转本身是O（1）操作，因为你只是移动指针。



#####HashMap在并发情况下会出现什么现象

1、多线程put操作后，get操作导致死循环。
2、多线程put非NULL元素后，get操作得到NULL值。
3、多线程put操作，导致元素丢失。

http://www.cnblogs.com/ITtangtang/p/3966467.html

##### put确定元素插入位置

采用如下的二进制计算方式

`(n - 1) & hash` ,n 就是数组的长度，

如果数组长度是16，换算成二进制，就是00000001111

前面全是0，所以使用 & 能得到桶位

##### resize（扩容）

jkd 8 `HashMap` 扩容，关键点是如下的代码：

```java
do {
	next = e.next;
	if ((e.hash & oldCap) == 0) { //关键点1，确定是否还在原来的位置上
		if (loTail == null)
			loHead = e;
		else
			loTail.next = e;
		loTail = e;
	}
	else {
		if (hiTail == null)
			hiHead = e;
		else
			hiTail.next = e;
		hiTail = e;
	}
} while ((e = next) != null);
if (loTail != null) {
	loTail.next = null;
	newTab[j] = loHead;
}
if (hiTail != null) {
	hiTail.next = null;
	newTab[j + oldCap] = hiHead;//关键点2：如果是高位，就要加上以前的容量
}
```

我们来看看关键点1的原理：

要想明白关键点1 的原理，我们需要结合上面的问题`put确定元素插入位置`

因为我们扩容了，那么从16扩展到32，我们求数据的位置，就是 1 1111 & hash

- 但是因为，hash是固定不变的，那么 hash & 1111（四位）是不变的，
- 那么`(e.hash & oldCap) == 0`就是表示，hash & 1 0000 （就是 32-1 相对于 16-1 多出来的第五位）为0，那么就是 hash & 1 1111 = hash & 1111 ，**所以就还是原来的位置**



#### LinkedHashMap 

##### 使用`LinkedHashMap ` 实现一个LRU cache

```java
class LRUCache<K, V> extends LinkedHashMap<K, V> {
    private final int CACHE_SIZE;

    /**
     * 传递进来最多能缓存多少数据
     *
     * @param cacheSize 缓存大小
     */
    public LRUCache(int cacheSize) {
        // true 表示让 LinkedHashMap 按照访问顺序来进行排序，最近访问的放在头部，最老访问的放在尾部。
        super((int) Math.ceil(cacheSize / 0.75) + 1, 0.75f, true);
        CACHE_SIZE = cacheSize;
    }

    @Override
    protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
        // 当 map 中的数据量大于指定的缓存个数的时候，就自动删除最老的数据。
        return size() > CACHE_SIZE;
    }
    
}
```



####HashMap和HashTable区别

- 1、Hashtable 继承 Dictionary ，HashMap 继承的是 Java2 出现的 Map 接口。
- 2、HashMap 去掉了 Hashtable 的 contains 方法，但是加上了 containsValue 和 containsKey 方法。
- 3、HashMap 允许空键值，而 Hashtable 不允许。
- 4、【重点】4、HashTable 是同步的，而 HashMap 是非同步的，效率上比 HashTable 要高。也因此，HashMap 更适合于单线程环境，而 HashTable 适合于多线程环境。
- 5、HashMap 的迭代器（Iterator）是 fail-fast 迭代器，HashTable的 enumerator 迭代器不是 fail-fast 的。
- 6、HashTable 中数组默认大小是 11 ，扩容方法是 `old * 2 + 1` ，HashMap 默认大小是 16 ，扩容每次为 2 的指数大小。

一般现在不建议用 HashTable 。主要原因是两点：

- 一是，HashTable 是遗留类，内部实现很多没优化和冗余。
- 二是，即使在多线程环境下，现在也有同步的 ConcurrentHashMap 替代，没有必要因为是多线程而用 Hashtable 。





####ConcurrentHashmap是怎么做到线程安全的

1.7是基于 分段锁，也就是 `Segment`      ,这个类继承与        `ReentrantLock` ，其put方法，火尝试获取锁

1.8中放弃了`Segment`臃肿的设计，取而代之的是采用`Node` + `CAS` + `Synchronized`来保证并发安全进行实现，





####**ConcurrentHashmap的size实现**

在 jdk 1.8 提供了一个新的计数方法`mappingCount `, 它以长整型long返回map中映射的数目。我们应该尽量使用这个新方法，而不是老的size方法， size方法返回的类型为int。 











**hashmap put 方法存放的时候怎么判断是否重复**



### Set

#### HashSet 的实现原理

1. 对于 HashSet 而言，它是基于 HashMap 实现的，底层采用 HashMap 来保存元素
2. 所有的value都是同一个对象`PRESENT` ，就是new 的一个Object

直接看源码：

```java
//构造方法
public HashSet(int initialCapacity) {
    map = new HashMap<>(initialCapacity);
}

//add 方法
private static final Object PRESENT = new Object();
public boolean add(E e) {
    return map.put(e, PRESENT)==null;
}
```



###其他

**Object的hashcode方法重写了，equals方法要不要改？**

要，就比如说HashMap，在put的时候判断，是不是同一个对象，就是，先比较hash code，然后比较是否是直接指向同一个对象，或者equals方法

```java
if (e.hash == hash &&
    ((k = e.key) == key || (key != null && key.equals(k))))
  break;
```



------

**JDK中有那几个线程池，顺带把线程池讲下？**

```
FixedThreadPool
CachedThreadPool
ScheduledThreadPool
SingleThreadExecutor
```

内部都是基于 `ThreadPoolExecutor` 来实现的

几个重要的参数

```
int corePoolSize,
int maximumPoolSize,
long keepAliveTime,
TimeUnit unit,
BlockingQueue<Runnable> workQueue,
ThreadFactory threadFactory
```

------

object类中的方法？

总共12个方法

wait（三个）、notify、notifyAll、hashcode、equles、toString、clone、getClass、finalize、registerNatives

------

**反射的机制**





## 线程与并发

###偏理论

####Happens -Before原理



####volatile 实现原理



#### ReentrantLock与 Synchronized 的区别？

- API方面:synchronized既可以修饰方法，也可以修饰代码块。ReentrantLock只能在方法体中使用。

- 公平锁:synchronized的锁是非公平锁，ReentrantLock默认情况下也是非公平锁，但可以通过带布尔值的构造函数要求使用公平锁。

- 等待可中断：假如业务代码中有两个线程，Thread1 Thread2。假设 Thread1 获取了对象object的锁，Thread2将等待Thread1释放object的锁。

- - 使用synchronized。如果Thread1不释放，Thread2将一直等待，不能被中断。synchronized也可以说是Java提供的原子性内置锁机制。内部锁扮演了互斥锁（mutual exclusion lock ，mutex）的角色，一个线程引用锁的时候，别的线程阻塞等待。
  - 使用ReentrantLock。如果Thread1不释放，Thread2等待了很长时间以后，可以中断等待，转而去做别的事情。至于判断重入锁，ReenTrantLock的字面意思就是再进入的锁，其实synchronized关键字所使用的锁也是可重入的，两者关于这个的区别不大。两者都是同一个线程没进入一次，锁的计数器都自增1，所以要等到锁的计数器下降为0时才能释放锁。

#### Lock 接口比 synchronized 块的优势是什么？





####  锁机制有什么用

#### 什么是乐观锁（Optimistic Locking）？如何实现乐观锁？如何避免ABA问题

解释以下名词：重排序，自旋锁，偏向锁，轻量级锁，可重入锁，公平锁，非公平锁，乐观锁，悲观锁

#### 什么时候应该使用可重入锁

简述锁的等级方法锁、对象锁、类锁

Java中活锁和死锁有什么区别？

什么是死锁(Deadlock)？导致线程死锁的原因？如何确保 N 个线程可以访问 N 个资源同时又不导致死锁

死锁与活锁的区别，死锁与饥饿的区别

#### 怎么检测一个线程是否拥有锁



#### 有哪些无锁数据结构，他们实现的原理是什么

读写锁可以用于什么应用场景

#### Executors类是什么？Executor和Executors的区别

什么是Java线程转储(Thread Dump)，如何得到它

#### 如何在Java中获取线程堆栈

#### 说出 3 条在 Java 中使用线程的最佳实践

1. 在使用线程或者线程组是最好给线程定义名字
2. 线程资源通过线程池提供 ，而不是自行显示创建

#### 在线程中你怎么处理不可捕捉异常

给线程设置`UncaughtExceptionHandler` 即不可捕捉异常处理器，这个`Handler` 是Thread的内部接口，可以通过`Thread` 的 `setUncaughtExceptionHandler` 方法设置

实际项目中使用多线程举例。你在多线程环境中遇到的常见的问题是什么？你是怎么解决它的

请说出与线程同步以及线程调度相关的方法

程序中有3个 socket，需要多少个线程来处理

假如有一个第三方接口，有很多个线程去调用获取数据，现在规定每秒钟最多有 10 个线程同时调用它，如何做到

如何在 Windows 和 Linux 上查找哪个线程使用的 CPU 时间最长

如何确保 main() 方法所在的线程是 Java 程序最后结束的线程

非常多个线程（可能是不同机器），相互之间需要等待协调才能完成某种工作，问怎么设计这种协调方案

你需要实现一个高效的缓存，它允许多个用户读，但只允许一个用户写，以此来保持它的完整性，你会怎样去实现它

#### 说说 Java 内存模型

###并发包

#### CLH同步队列是怎么实现非公平和公平的？

首先解释CLH 就是 AQS 内部维护的一个FIFO队列，该队列就是CLH同步队列。

CLH同步队列是一个FIFO双向队列，AQS依赖它来完成同步状态的管理，当前线程如果获取同步状态失败时，AQS则会将当前线程已经等待状态等信息构造成一个节点（Node）并将其加入到CLH同步队列，同时会阻塞当前线程，当同步状态释放时，会把首节点唤醒（公平锁），使其再次尝试获取同步状态

 在CLH同步队列中，一个节点表示一个线程，它保存着线程的引用（thread）、状态（waitStatus）、前驱节点（prev）、后继节点（next） 

#### Atomic 类的原理是

1. 包装的值使用volitale修饰
2. 里面的一些操作使用`死循环`+unsafe的`cas`操作，比如自减操作

```java
public final int getAndDecrement() {
  for (;;) {
    int current = get();
    int next = current - 1;
    if (compareAndSet(current, next))
      return current;
  }
}
```





####CyclicBarrier和CountDownLatch的区别

CountDownLatch：一个或者多个线程，等待其他多个线程完成某件事情之后才能执行

CyclicBarrier：多个线程互相等待，直到到达同一个同步点，再继续**一起执行**

```java
public class CyclicBarrierTest {
    public static void main(String[] args) {
        CyclicBarrier barrier = new CyclicBarrier(3);
        for(int i = 0; i < barrier.getParties(); i++){
            new Thread(new MyRunnable(barrier), "队友"+i).start();
        }
        System.out.println("main function is finished.");
    }


    private static class MyRunnable implements Runnable{
        private CyclicBarrier barrier;

        public MyRunnable(CyclicBarrier barrier){
            this.barrier = barrier;
        }

        @Override
        public void run() {
            for(int i = 0; i < 3; i++) {
                try {
                    Random rand = new Random();
                    int randomNum = rand.nextInt((3000 - 1000) + 1) + 1000;//产生1000到3000之间的随机整数
                    Thread.sleep(randomNum);
                    System.out.println(Thread.currentThread().getName() + ", 通过了第"+i+"个障碍物, 使用了 "+((double)randomNum/1000)+"s");
                    this.barrier.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

执行结果：

```
main function is finished.
队友1, 通过了第0个障碍物, 使用了 1.432s
队友0, 通过了第0个障碍物, 使用了 1.465s
队友2, 通过了第0个障碍物, 使用了 2.26s
队友1, 通过了第1个障碍物, 使用了 1.542s
队友0, 通过了第1个障碍物, 使用了 2.154s
队友2, 通过了第1个障碍物, 使用了 2.556s
队友1, 通过了第2个障碍物, 使用了 1.426s
队友2, 通过了第2个障碍物, 使用了 2.603s
队友0, 通过了第2个障碍物, 使用了 2.784s
```

#### ArrayBlockingQueue

关键点：

1. 使用`final Object[] items;`来存放数据，使用`int takeIndex;`, 和 `int putIndex;`来分别记录存和取的记录，使用`int count;`来记录队列中数据的数量



#### ReadWriteLock是什么？

这是Java并发包下面的一个接口，`ReadWriteLock`, 就两个方法`readLock` 和 `writeLock` ，实现类`ReentrantReadWriteLock` ，应用在一些读写分离的场景

### 实操题

#### 两个线程交替执行，一个输出奇数，一个输出偶数

我们使用 volatile 变量代替 CAS 变量，减轻使用 CAS 的消耗，注意，这里 ++num 不是原子的，但不妨碍，因为有 flag 变量控制。而 num 必须是 volatile 的，如果不是，会导致可见性问题。

```java
public class ThreadPrintDemo3 {
    
    static volatile int num = 0;
    static volatile boolean flag = false;

    public static void main(String[] args) {

        Thread t1 = new Thread(() -> {
            for (; 100 > num; ) {
                if (!flag && (num == 0 || ++num % 2 == 0)) {
                    System.out.println(Thread.currentThread() + " ：" + num);
                    flag = true;
                }
            }
        }
        );

        Thread t2 = new Thread(() -> {
            for (; 100 > num; ) {
                if (flag && (++num % 2 != 0)) {
                    System.out.println(Thread.currentThread() + " ：" + num);
                    flag = false;
                }
            }
        }
        );
        t1.start();
        t2.start();
    }
}
```

### 用过读写锁吗，原理是什么，一般在什么场景下用

### ThreadLocal

#### ThreadLocal 实现原理

### ConcurrentHashMap

面试者可以先说历史，1.8之前采用分段锁，核心就是一句话：尽量降低同步锁的粒度。1.8之后使用CAS思想代替冗杂的分段锁实现。不出意料，面试者答出CAS之后必定会被追问其思想以及应用，换做我自己的话会有如下思路作答：CAS采用乐观锁思想达到lock-free，提一下sun.misc.Unsafe中的native方法，至于CAS的其他应用可以聊一聊Atomic原子类和一些无锁并发框架（如Amino），提到ABA问题加分。

##数据库



###Mysql

#### B+树、B-树的区别

首先B树就是B-树

**一个 m 阶的B树是一个有以下属性的树：**

1. 每一个节点最多有 m 个子节点
2. 每一个非叶子节点（除根节点）最少有 ⌈m/2⌉ 个子节点
3. 如果根节点不是叶子节点，那么它至少有两个子节点
4. 有 k 个子节点的非叶子节点拥有 k − 1 个键
5. 所有的叶子节点都在同一层

**B树和B+树的区别**

1. B+树中只有叶子节点会带有指向记录的指针，而B树则所有节点都带有，在内部节点出现的索引项不会再出现在叶子节点中。
2. B+树中所有叶子节点都是通过指针连接在一起，而B树不会。

**B+树优点：**

1. 中间节点全是索引节点，一个是可以降低树的高度，另一个是一个中间节点可以索引到更多的记录
2. 可以直接在叶子节点层横向遍历，b树想要遍历则需要叶子节点和上层节点不停往返。



####查看SQL是不是使用了索引，有哪些工具

mysql 的 explain 命令，字段key如果为null ，表示没有使用索引



#### select for update 是什么含义，会锁表还是锁行或是其他



####SQL优化的常见方法有哪些

1. 对查询进行优化，应尽量避免全表扫描，首先应考虑在 where 及 order by 涉及的列上建立索引。 
2. 应尽量避免在 where 子句中对字段进行 null 值判断，否则将导致引擎放弃使用索引而进行全表扫描，如：select id from t where num is null可以在num上设置默认值0，确保表中num列没有null值，然后这样查询：select id from t where num=0 
3. 应尽量避免在 where 子句中使用!=或<>操作符，否则引擎将放弃使用索引而进行全表扫描。 
4. 应尽量避免在 where 子句中使用or 来连接条件，否则将导致引擎放弃使用索引而进行全表扫描，如：select id from t where num=10 or num=20可以这样查询：select id from t where num=10 union all select id from t where num=20 
5. in 和 not in 也要慎用，否则会导致全表扫描，如：select id from t where num in(1,2,3) 对于连续的数值，能用 between 就不要用 in 了：select id from t where num between 1 and 3 
6. 避免使用通配符。下面的查询也将导致全表扫描：select id from t where name like ‘李%’若要提高效率，可以考虑全文检索。 
7. 如果在 where 子句中使用参数，也会导致全表扫描。因为SQL只有在运行时才会解析局部变量，但优化程序不能将访问计划的选择推迟到运行时；它必须在编译时进行选择。然而，如果在编译时建立访问计划，变量的值还是未知的，因而无法作为索引选择的输入项。如下面语句将进行全表扫描：select id from t where num=@num可以改为强制查询使用索引：select id from t with(index(索引名)) where num=@num 
8. 应尽量避免在 where 子句中对字段进行表达式操作，这将导致引擎放弃使用索引而进行全表扫描。如：select id from t where num/2=100应改为:select id from t where num=100*2 
9. 应尽量避免在where子句中对字段进行函数操作，这将导致引擎放弃使用索引而进行全表扫描。如：select id from t where substring(name,1,3)=’abc’ ，name以abc开头的id应改为:select id from t where name like ‘abc%’ 
10. 不要在 where 子句中的“=”左边进行函数、算术运算或其他表达式运算，否则系统将可能无法正确使用索引
11. 在使用索引字段作为条件时，如果该索引是复合索引，那么必须使用到该索引中的第一个字段作为条件时才能保证系统使用该索引，否则该索引将不会被使用，并且应尽可能的让字段顺序与索引顺序相一致。 
12. 不要写一些没有意义的查询，如需要生成一个空表结构：select col1,col2 into #t from t where 1=0 这类代码不会返回任何结果集，但是会消耗系统资源的，应改成这样：create table #t(…) 
13. 很多时候用 exists 代替 in 是一个好的选择：select num from a where num in(select num from b)用下面的语句替换：select num from a where exists(select 1 from b where num=a.num) 
14. 并不是所有索引对查询都有效，SQL是根据表中数据来进行查询优化的，当索引列有大量数据重复时，SQL查询可能不会去利用索引，如一表中有字段sex，male、female几乎各一半，那么即使在sex上建了索引也对查询效率起不了作用。 
15. 索引并不是越多越好，索引固然可以提高相应的 select 的效率，但同时也降低了insert 及 update 的 效率，因为 insert 或 update 时有可能会重建索引，所以怎样建索引需要慎重考虑，视具体情况而定。一个表的索引数最好不要超过6个，若太多则应考虑一些不常使用到的列上建的索引是否有 必要。 
16. 应尽可能的避免更新 clustered 索引数据列，因为 clustered 索引数据列的顺序就是表记录的物理存储 顺序，一旦该列值改变将导致整个表记录的顺序的调整，会耗费相当大的资源。若应用系统需要频繁更新 clustered 索引数据列，那么需要考虑是否应将该索引建为 clustered 索引。 
17. 尽量使用数字型字段，若只含数值信息的字段尽量不要设计为字符型，这会降低查询和连接的性能，并会增加存储开销。这是因为引擎在处理查询和连接时会逐个比较字符串中每一个字符，而对于数字型而言只需要比较一次就够了。 
18. 尽可能的使用 varchar/nvarchar 代替 char/nchar ，因为首先变长字段存储空间小，可以节省存储空间，其次对于查询来说，在一个相对较小的字段内搜索效率显然要高些。 
19. 任何地方都不要使用 select * from t ，用具体的字段列表代替“*”，不要返回用不到的任何字段。 
20. 尽量使用表变量来代替临时表。如果表变量包含大量数据，请注意索引非常有限（只有主键索引）。 
21. 避免频繁创建和删除临时表，以减少系统表资源的消耗。 
22. 临时表并不是不可使用，适当地使用它们可以使某些例程更有效，例如，当需要重复引用大型表或常用表中的某个数据集时。但是，对于一次性事件，最好使用导出表。 
23. 在新建临时表时，如果一次性插入数据量很大，那么可以使用 select into 代替 create table，避免造成大量 log ，以提高速度；如果数据量不大，为了缓和系统表的资源，应先create table，然后insert。 
24. 如果使用到了临时表，在存储过程的最后务必将所有的临时表显式删除，先 truncate table ，然后 drop table ，这样可以避免系统表的较长时间锁定。 
25. 尽量避免使用游标，因为游标的效率较差，如果游标操作的数据超过1万行，那么就应该考虑改写。 
26. 使用基于游标的方法或临时表方法之前，应先寻找基于集的解决方案来解决问题，基于集的方法通常更有效。 
27. 与临时表一样，游标并不是不可使用。对小型数据集使用 FAST_FORWARD 游标通常要优于其他逐行处理方法，尤其是在必须引用几个表才能获得所需的数据时。在结果集中包括“合计”的例程通常要比使用游标执行的速度快。如果开发时间允许，基于游标的方法和基于集的方法都可以尝试一下，看哪一种方法的效果更好。 
28. 在所有的存储过程和触发器的开始处设置 SET NOCOUNT ON ，在结束时设置 SET NOCOUNT OFF。无需在执行存储过程和触发器的每个语句后向客户端发送DONE_IN_PROC 消息。 
29. 尽量避免大事务操作，提高系统并发能力。 
30. 尽量避免向客户端返回大数据量，若数据量过大，应该考虑相应需求是否合理。


#### 聚集索引和非聚集索引的区别

聚集索引(聚簇索引或者主键索引):叶子节点存放的是整张表的行记录数据

非聚集索引(二级索引或者非主键索引):叶子节点内容是主键的值,所以使用此类索引后，**回到主键索引树搜索的过程，我们称为回表**



#### 索引什么时候会失效变成全表扫描

#### Mysql性能优化

**可以从如下的方向提升mysql的性能：**

设计数据库时：数据库表、字段的设计，存储引擎

利用好MySQL自身提供的功能，如索引等

横向扩展：MySQL集群、负载均衡、读写分离

SQL语句的优化（收效甚微）



1. 增大 join_buffer_size 的数值，也就是表和表联接的缓冲区的大小，在做join操作的时候会把驱动表的相应数据加载进入  joinbuffer 中， 在使用join的时候，基于被驱动表是否有索引有两种情况，分别是 Index Nested-Loop Join  和 Block Nested-Loop Join，后者表示没有索引，如果在后者的情况，如果join-buffer 不够大，一次只能加载部分的驱动表，但是相应的被驱动表就要全表扫描一次，所以增加此buffer 能降低被驱动表的全表扫描次数从而提升性能

**SQL索引的顺序，字段的顺序**

基于索引的最左匹配特性？



####什么是 MVCC ？

多版本并发控制（MVCC **Multi-Version Concurrency Control**），是一种用来**解决读-写冲突**的无锁并发控制，也就是为事务分配单向增长的时间戳，为每个修改保存一个版本，版本与事务时间戳关联，读操作只读该事务开始前的数据库的快照。 这样在读操作不用阻塞写操作，写操作不用阻塞读操作的同时，避免了脏读和不可重复读。

//TODO

------

**不可重复都出现在什么场景**

------

**mysql事物合隔离性和隔离级别**

------

####  如果你发现你的sql语句始终走另一个索引，但是你希望它走你想要的索引，怎么办？ 

在查询语句，表的后面加上 use index(XXX)，比如：

```sql
select emp_no,salary from salaries use index(s_f_t) where emp_no<11010 and salary<60000
```

### Redis

#### 为什么 Redis 单线程模型也能效率这么高？

- 1、纯内存操作。

  > Redis 为了达到最快的读写速度，将数据都读到内存中，并通过异步的方式将数据写入磁盘。所以 Redis 具有快速和数据持久化的特征。
  >
  > 如果不将数据放在内存中，磁盘 I/O 速度为严重影响 Redis 的性能。

- 2、核心是基于非阻塞的 IO 多路复用机制。

- 3、单线程反而避免了多线程的频繁上下文切换问题。

  > Redis 利用队列技术，将并发访问变为串行访问，消除了传统数据库串行控制的开销

- 4、Redis 全程使用 hash 结构，读取速度快，还有一些特殊的数据结构，对数据存储进行了优化，如压缩表，对短数据进行压缩存储，再如，跳表，使用有序的数据结构加快读取的速度。

#### Redis 有几种数据“过期”策略？

Redis 的过期策略，就是指当 Redis 中缓存的 key 过期了，Redis 如何处理。

Redis 提供了 3 种数据过期策略：

- 被动删除：当读/写一个已经过期的 key 时，会触发惰性删除策略，直接删除掉这个过期 key 。
- 主动删除：由于惰性删除策略无法保证冷数据被及时删掉，所以 Redis 会定期主动淘汰一批已过期的 key 。
- 主动删除：当前已用内存超过 maxmemory 限定时，触发主动清理策略，即 [「Redis 有哪几种数据“淘汰”策略？」](http://svip.iocoder.cn/Redis/Interview/#) 。

在 Redis 中，同时使用了上述 3 种策略，即它们**非互斥**的。



#### Redis 有哪几种数据“淘汰”策略？

Redis 内存数据集大小上升到一定大小的时候，就会进行数据淘汰策略。

Redis 提供了 6 种数据淘汰策略：

1. volatile-lru
2. volatile-ttl
3. volatile-random
4. allkeys-lru
5. allkeys-random
6. no-enviction

#### 讲一下Redis分布式锁的实现 



## 中间件

### RocketMQ

#### Producer 发送消息有几种方式？

Producer 发送消息，有三种方式：

1. 同步方式
2. 异步方式
3. Oneway 方式

其实就是对应`CommunicationMode` 的三种枚举



#### 消息队列有几种投递方式？分别有什么优缺点

消息队列有 **push 推送**和 **pull 拉取**两种投递方式

- push 
  - 优点，就是及时性。
  - 缺点，就是受限于消费者的消费能力，可能造成消息的堆积，Broker 会不断给消费者发送不能处理的消息。
- pull
  - 优点，就是主动权掌握在消费方，可以根据自己的消息速度进行消息拉取。
  - 缺点，就是消费方不知道什么时候可以获取的最新的消息，会有消息延迟和忙等。

目前的消息队列，**基于 push + pull 模式结合的方式**，Broker 仅仅告诉 Consumer 有新的消息，具体的消息拉取，还是 Consumer 自己主动拉取。

#### 什么是消息重试？如何实现？

#### RocketMQ 消息模式(MessageModel)

```java
public enum MessageModel {
    /**
     * broadcast
     */
    BROADCASTING("BROADCASTING"),
    /**
     * clustering
     */
    CLUSTERING("CLUSTERING");
}
```

在 Clustering 模式下，同一个 ConsumerGroup ( GroupName 相同 ） 里的每个 Consumer 只消费所订阅消息 的一部分 内 容， 同一个 ConsumerGroup 里所有的 Consumer 消 费 的内 容合起来才是所订阅 Topic 内 容 的 整体 ，从而达到负载均衡的目的 。 

在 Broadcasting 模式下，同一个 ConsumerGroup 里的每个 Consumer 都能消费到所订阅 Topic 的全部消息，也就是一个消息会被多次分发，被多个 Consumer 消费 。 

#### RocketMQ负载均衡

RocketMQ中的负载均衡都在Client端完成，具体来说的话，主要可以分为Producer端发送消息时候的负载均衡和Consumer端订阅消息的负载均衡。

#####  Producer的负载均衡

Producer端在发送消息的时候，会先根据Topic找到指定的TopicPublishInfo，在获取了TopicPublishInfo路由信息后，RocketMQ的客户端在默认方式下selectOneMessageQueue()方法会从TopicPublishInfo中的messageQueueList中选择一个队列（MessageQueue）进行发送消息。具体的容错策略均在MQFaultStrategy这个类中定义。这里有一个sendLatencyFaultEnable开关变量，如果开启，在随机递增取模的基础上，再过滤掉not available的Broker代理。所谓的"latencyFaultTolerance"，是指对之前失败的，按一定的时间做退避。例如，如果上次请求的latency超过550Lms，就退避3000Lms；超过1000L，就退避60000L；如果关闭，采用随机递增取模的方式选择一个队列（MessageQueue）来发送消息，latencyFaultTolerance机制是实现消息发送高可用的核心关键所在。

### Nginx 

#### Nginx负载均衡时是如何判断某个节点挂掉了？

### Kafka

#### 为什么kafka这么快，又能保证消息不丢失？

##### 如何做到消息不丢失

**ACK 机制**

通过 ACK 机制保证消息送达。Kafka 采用的是至少一次（At least once），消息不会丢，但是可能会重复传输。

**发送消息**

为了得到更好的性能，Kafka 支持在生产者一侧进行本地buffer，也就是累积到一定的条数才发送，如果这里设置不当是会丢消息的。

生产者端设置 producer.type=async, sync，默认是 sync。

当设置为 async，会大幅提升性能，因为生产者会在本地缓冲消息，并适时批量发送。

如果对可靠性要求高，那么这里可以设置为 sync 同步发送。

**消费消息**

如果更注重可靠性，则需要显示提交 Offset，也就是当所有业务都处理完成的时候，再提交 Offset。这样会导致重复消费，需要提供幂等性接口。

##### 为什么 Kafka 性能高？

**顺序写磁盘**

顺序写磁盘的性能是随机写入的性能的6000倍的提升，媲美内存随机访问的性能，磁盘不再是瓶颈点。

**Page Cache**

为了优化读写性能，Kafka利用了操作系统本身的Page Cache，就是利用操作系统自身的内存而不是JVM空间内存。通过操作系统的Page Cache，Kafka的读写操作基本上是基于内存的，读写速度得到了极大的提升。

**零拷贝技术**

零拷贝技术，可以有效的减少上下文切换和拷贝次数。

kafka的设计实现，涉及到很多的底层技术，为了能够把它吃透，需要花大量的时间，大量的精力。

#### 为什么Kafka不支持读写分离？

### Zookeeper

#### Zookeeper的java客户端你使用过哪些?

zkClient，Curator

Dubbo 使用的是zkClient



## 算法与数据结构

**说下你知道的排序算法**

### 红黑树

**红黑树特点:**

1.节点是红色或黑色。

2.根节点是黑色。

3.每个叶子节点都是黑色的空节点（NULL节点）。

4 每个红色节点的两个子节点都是黑色。(从每个叶子到根的所有路径上不能有两个连续的红色节点)

5.从任一节点到其每个叶子的所有路径都包含相同数目的黑色节点。



**红黑树的应用：**

TreeMap、TreeSet以及JDK1.8之后的HashMap底层都用到了红黑树，以及Linux虚拟内存的管理，都是通过红黑树去实现的。

不错的文章：

[寻找红黑树的操作手册](http://dandanlove.com/2018/03/18/red-black-tree/)

[红黑树深入剖析及Java实现-美团](https://zhuanlan.zhihu.com/p/24367771)

[漫画：什么是红黑树？](https://juejin.im/post/5a27c6946fb9a04509096248#comment)

### 布隆过滤器

1. 首先是一个bigmap来存放
2. 多个Hash 函数来hash Key，把每个hash结果对应位置置为0，
3. 在做isExist操作的时候，如果多个hash结果位置上有一个为0，就表示此key不存在
4. 布隆过滤器，只能确定不存在和可能存在，不能删除key

### 给一个set打印出所有子集

### 多线程从多个文件中读入数据，写到同一个文件中

### 判断ip是否在给定范围内

### 打乱一副扑克牌，不能用额外空间，证明为什么是随机的



## 缓存

### 请说说有哪些缓存算法？是否能手写一下 LRU 代码的实现？

缓存算法，比较常见的是三种：

- LRU（least recently used ，最近最少使用)
- LFU（Least Frequently used ，最不经常使用)
- FIFO（first in first out ，先进先出)

### 用了缓存之后，有哪些常见问题？

常见的问题，可列举如下：

- 缓存何时**写入**？并且写时如何避免并发重复写入？
- 缓存如何**失效**？
- 缓存和 DB 的**一致性**如何保证？
- 如何避免缓存**穿透**的问题？
- 如何避免缓存**击穿**的问题？ 
- 如果避免缓存**雪崩**的问题？

### 如果避免缓存”穿透”的问题？

#### 缓存穿透

缓存穿透，是指查询一个一定不存在的数据，由于缓存是不命中时被动写( *被动写，指的是从 DB 查询到数据，则更新到缓存中* )的，并且处于容错考虑，如果从 DB 查不到数据则不写入缓存，这将导致这个不存在的数据每次请求都要到 DB 去查询，失去了缓存的意义。

#### 如何解决

有两种方案可以解决：

- 方案一，缓存空对象：当从 DB 查询数据为空，我们仍然将这个空结果进行缓存，具体的值需要使用特殊的标识，能和真正缓存的数据区分开。另外，需要设置较短的过期时间，一般建议不要超过 5 分钟。
- 方案二，BloomFilter 布隆过滤器：在缓存服务的基础上，构建 BloomFilter 数据结构，在 BloomFilter 中存储对应的 KEY 是否存在，如果存在，说明该 KEY 对应的值为空。那么整个逻辑的如下：
  - 1、根据 KEY 查询缓存。如果存在对应的值，直接返回；如果不存在，继续向下执行。
  - 2、根据 KEY 查询在缓存 BloomFilter 的值。如果存在值，说明该 KEY 不存在对应的值，直接返回空；如果不存在值，继续向下执行。
  - 3、查询 DB 对应的值，如果存在，则更新到缓存，并返回该值。如果不存在值，更新到 缓存 BloomFilter 中，并返回空。

🦅 **选择**

这两个方案，各有其优缺点。

|      | 缓存空对象                            | BloomFilter 布隆过滤器        |
| ---- | -------------------------------- | ------------------------ |
| 适用场景 | 1、数据命中不高   2、保证一致性               | 1、数据命中不高   2、数据相对固定、实时性低 |
| 维护成本 | 1、代码维护简单   2、需要过多的缓存空间   3、数据不一致 | 1、代码维护复杂   2、缓存空间占用小     |

- 对于 BloomFilter 布隆过滤器，使用场景中的“数据相对固定、实时性低” ，TODO 不支持删除？

实际情况下，使用方案二比较多。因为，相比方案一来说，更加节省内容，对缓存的负荷更小。并且，常用的缓存 Redis 支持构建 BloomFilter 数据结构，具体可以看看如下文章：

## JVM

###JVM 垃圾回收算法？

1. 标记-清除算法
2. 标记-整理算法
3. 复制算法
4. 分代收集算法

###JVM垃圾回收机制，何时触发MinorGC等操作

对象优先在新生代 Eden 区中分配，如果 Eden 区没有足够的空间时，就会触发一次 Young GC 。



###什么情况下会出现 Full GC？

- 1、在执行 Young GC 之前，JVM 会进行空间分配担保——如果老年代的连续空间小于新生代对象的总大小（或历次晋升的平均大小），则触发一次 Full GC 。
- 2、大对象直接进入老年代，从年轻代晋升上来的老对象，尝试在老年代分配内存时，但是老年代内存空间不够。
- 3、显式调用 `System#gc()` 方法时。//建议jvm执行fullgc，并不一定会执行 
- 4、执行了jmap -histo:live pid命令  //这个会立即触发fullgc 



###直接内存（堆外内存）与堆内存比较？

1. 直接内存申请空间耗费更高的性能，当频繁申请到一定量时尤为明显。
2. 直接内存 IO 读写的性能要优于普通的堆内存，在多次读写操作的情况下差异明显。



###内存泄漏的可能原因

1. 静态集合类引起内存泄漏，如HashMap，ArrayList等
2. 各种连接，比如数据库连接（dataSourse.getConnection()），网络连接(socket)和io连接，除非其显式的调用了其close（）方法将其连接关闭，否则是不会自动被GC 回收的。
3. 单例模式，不正确使用单例模式是引起内存泄漏的一个常见问题，单例对象在初始化后将在JVM的整个生命周期中存在（以静态变量的方式），如果单例对象持有外部的引用。



### 当出现了内存溢出，你怎么排错

首先查看错误日志，确定是哪儿溢出了，堆、栈、永久代等等

1. +HeapDumpOnOutOfMemoryError参数让我们看到了溢出后的堆内存情况
2. 使用MAT导入堆文件
3. MAT工具，有个**Leak Suspects** 按钮或者查看最大的堆内存占用，都能慢慢帮助我们找到占用最多的对象

下面说说一些不同区域的溢出可能的情况：

**方法区溢出**

方法区用于存放Class的相关信息，如类名、访问修饰符、常量池、字段描述、方法描述等，所以在一些大量产生代理类的时候，容易溢出，比如Cglib

**永久代溢出**

### Java内存泄漏

我的理解是GC Root 可达的对象已经无用了是垃圾对象，但是因为被牵引无法被回收，一般都是间接被牵引的，比如如下的情况：

- Hibernate 的 Session（一级缓存）中的对象属于持久态，垃圾回收器是不会回收这些对象的，然而这些对象中可能存在无用的垃圾对象。
- 使用 Netty 的堆外的 ByteBuf 对象，在使用完后，并未归还，导致使用的一点一点在泄露。

### 什么是安全点？

SafePoint 安全点，顾名思义是指一些特定的位置，当线程运行到这些位置时，线程的一些状态可以被确定(the thread’s representation of it’s Java machine state is well described)，比如记录OopMap 的状态，从而确定 GC Root 的信息，使 JVM 可以安全的进行一些操作，比如开始 GC 。

SafePoint 指的特定位置主要有：

1. 循环的末尾 (防止大循环的时候一直不进入 Safepoint ，而其他线程在等待它进入 Safepoint )。
2. 方法返回前。
3. 调用方法的 Call 之后。
4. 抛出异常的位置。

详细的内容，可以看看 [《深入学习 JVM-JVM 安全点和安全区域》](https://my.oschina.net/wenbo123/blog/1822414) 。

### 什么是安全区域？

> 如果程序长时间不执行，比如线程调用的 sleep 方法，这时候程序无法响应 JVM 中断请求这时候线程无法到达安全点，显然 JVM 也不可能等待程序唤醒，这时候就需要安全区域了。
>
> 安全区域是指一段代码片中，引用关系不会发生变化，在这个区域任何地方 GC 都是安全的，安全区域可以看做是安全点的一个扩展。
>
> - 线程执行到安全区域的代码时，首先标识自己进入了安全区域，这样 GC 时就不用管进入安全区域的线程了.
> - 线程要离开安全区域时就检查 JVM 是否完成了 GC Roots 枚举（或者整个 GC 过程），如果完成就继续执行，如果没有完成就等待直到收到可以安全离开的信号。

### 对象分配规则是什么？

- 对象优先分配在 Eden 区。

  > 如果 Eden 区无法分配，那么尝试把活着的对象放到 Survivor0 中去（Minor GC）
  >
  > - 如果 Survivor0 可以放入，那么放入之后清除 Eden 区。
  > - 如果 Survivor0 不可以放入，那么尝试把 Eden 和 Survivor0 的存活对象放到 Survivor1 中。
  >   - 如果 Survivor1 可以放入，那么放入 Survivor1 之后清除 Eden 和 Survivor0 ，之后再把 Survivor1 中的对象复制到 Survivor0 中，保持 Survivor1 一直为空。
  >   - 如果 Survivor1 不可以放入，那么直接把它们放入到老年代中，并清除 Eden 和 Survivor0 ，这个过程也称为**分配担保**。
  >
  > ps：清除 Eden、Survivor 区，就是 Minor GC 。
  >
  > 总结来说，分配的顺序是：新生代（Eden => Survivor0 => Survivor1）=> 老年代

- 大对象直接进入老年代（大对象是指需要大量连续内存空间的对象）。

  > 这样做的目的是，避免在 Eden 区和两个 Survivor 区之间发生大量的内存拷贝（新生代采用复制算法收集内存）。

- 长期存活的对象进入老年代。

  > 虚拟机为每个对象定义了一个年龄计数器，如果对象经过了 1 次 Minor GC 那么对象会进入 Survivor 区，之后每经过一次 Minor GC 那么对象的年龄加 1 ，知道达到阀值对象进入老年区。

- 动态判断对象的年龄。

  > 为了更好的适用不同程序的内存情况，虚拟机并不是永远要求对象的年龄必须达到 MaxTenuringThreshold 才能晋升老年代。
  >
  > 如果 Survivor 区中相同年龄的所有对象大小的总和大于 Survivor 空间的一半，年龄大于或等于该年龄的对象可以直接进入老年代。

- 空间分配担保。

  > 每次进行 Minor GC 时，JVM 会计算 Survivor 区移至老年区的对象的平均大小，如果这个值大于老年区的剩余值大小则进行一次 Full GC ，如果小于检查 HandlePromotionFailure 设置，如果 `true` 则只进行 Monitor GC ，如果 `false` 则进行 Full GC 。

### 如何判断一个对象是否已经死去？

1. **引用计数**，此方法简单，无法解决对象相互循环引用的问题。目前在用的有 Python、ActionScript3 等语言。
2. 可达性分析

### 可作为 GC Roots 的对象包括以下几种

1. 虚拟机栈（栈帧中的本地变量表）中引用的对象。
2. 方法区中的类静态属性引用的对象。
3. 方法区中常量引用的对象。
4. 本地方法栈中 JNI(即一般说的 Native 方法)中引用的对象。

### JMM Java内存模型

JVM规范定义了Java内存模型(Java Memory Model) 来屏蔽掉各种操作系统，虚拟机实现厂商和硬件的内存访问差异，以确保Java程序在所有操作系统和平台上能够实现一次编写、到处运行的效果。

JMM 就是一组规则，这组规则意在解决在并发编程可能出现的线程安全问题，并提供了内置解决方案（happen-before原则）及其外部可使用的同步手段(synchronized/volatile/final等)，确保了程序执行在多线程环境中的应有的原子性，可视性及其有序性。

### 你们线上应用的 JVM 参数有哪些



### 类加载

#### Class.forName和ClassLoader加载类的区别

1. Class.forName可以指定是否初始化`public static Class<?> forName(String name, boolean initialize,ClassLoader loader)`,如果参数`initialize`为true，则加载的类将会被初始化也就是初始化一些静态块和静态方法的执行

2. 使用场景：在我们熟悉的Spring框架中的IOC的实现就是使用的ClassLoader。

   而在我们使用JDBC时通常是使用Class.forName()方法来加载数据库连接驱动。

   这是因为在JDBC规范中明确要求Driver(数据库驱动)类必须向DriverManager注册自己。`com.mysql.jdbc.Driver`类有有一块静态代码`DriverManager.registerDriver(new Driver());`





##Linux

### 用一行命令查看文件的最后五行

Tail -n 5 filename

### 用一行命令输出正在运行的 java 进程。

ps -ef|grep Java

## 分布式

### 理论



#### 一致性hash算法原理与应用

####  CAP原则

**所谓的CAP**  C强一致性  A可用性 P 分区容错性

一个分布式系统不可能同时满足C(一致性)、A(可用性)和P(分区容错性)。由于分区容错性P在是分布式系统中必须要保证的,因此我们只能在A和C之间进行权衡。 

##### Zookeeper 遵守 CP

当向注册中心查询服务列表时,    我们可以容忍注册中心返回的是几分钟以前的注册信息, 但不能接受服务直接down掉不可用。

也就是说,服务注册功能对一致性的要求要高于可用性。

但是zookeeper 会出现这样一种情况,   当 master节点因为网络故障与其他节点失去联系时,剩余节点会重新进行 leader选举。

问题在于,选举 leader的时间太长,30~120s,目选举期间整个zookeeper 集群都是不可用的,这就导致在选举期间注册服务瘫痪。

在云部署的环境下,因网络问题使得zookeeper 集群失去 master节点是较大概率会发生的事,虽然服务能够最终恢复,但是漫长的选举时间导致的注册长期不可用是不能容忍的。 

**用一种其他说法来说 就是** ：

   当有一个zookeeper  挂了  那其他的zookeeper 会进行 一次选举 （强一致性 ： 我一定要保持数据一致性）  而在此选举期间  zookeeper  是不可用的   而当前 有用户正在使用 用户就不爽了 。

#####  Eureka 遵守 AP

     Eureka:看明白了这一点,因此在设计时就优先保证可用性。

Eureka各个节点都是平等的,几个节点挂掉不会影响正常节点的工作,剩余的节点依然可以提供注册和查询服务。

而 Eureka的客户端在向某个 Eureka注册或时如果发现连接失败,则会自动切换至其它节点 

只要有一台 Eureka还在,就能保证注册服务可用(保证可用性),只不过查到的信息可能不是最新的不保证强一致性)。

除此之外, Eureka还有一种自我保护机制,如果在15分钟内超过85%的节点都没有正常的心跳,那么 Eurekas就认为客户端与注册中心出现了网络故障,此时会出现以下几种情况:

1. Eureka不再从注册列表中移除因为长时间没收到心跳而应该过期的服务

2. Ureka仍然能够接受新服务的注册和査询请求,但是不会被同步到其它节点上(即保证当前节点依然可用)

3.当网络稳定时,当前实例新的注册信息会被同步到其它节点中

因此, Eureka可以很好的应对因网络故障导致部分节点失去联系的情況,而不会像 zookeeper那样使整个注册服务瘫痪。 


#### 分布式raft算法

**概念**

Raft是实现分布式共识的一种算法，主要用来管理日志复制的一致性。它和Paxos的功能是一样，但是相比于Paxos，Raft算法更容易理解、也更容易应用到实际的系统当中。而Raft算法也是联盟链采用比较多的共识算法。

**Raft的三种状态（角色）**

Follower（群众）

被动接收Leader发送的请求。所有的节点刚开始的时候是处于Follower状态。

Candidate（候选人）

由Follower向Leader转换的中间状态

Leader（领导）

负责和客户端交互以及日志复制（日志复制是单向的，即Leader发送给Follower），同一时刻最多只有1个Leader存在。

##### 任期（Term）概念



#### 读写分离缺点

- **数据一致性问题**。数据从主节点转到从节点必然会有一个延时的时间窗口，这个时间 窗口会导致主从节点之间的数据不一致。某一时刻，在主节点和从节点中 A 数据的值都为 X， 之后将主节点中 A 的值修改为 Y，那么在这个变更通知到从节点之前，应用读取从节点中的 A 数据的值并不为最新的 Y，由此便产生了数据不一致的问题。
- **延时问题**。类似 Redis 这种组件，数据从写入主节点到同步至从节点中的过程需要经 历网络→主节点内存→网络→从节点内存这几个阶段，整个过程会耗费一定的时间。而在 Kafka 中，主从同步会比 Redis 更加耗时，它需要经历网络→主节点内存→主节点磁盘→网络→从节 点内存→从节点磁盘这几个阶段。对延时敏感的应用而言，主写从读的功能并不太适用。

#### 消息队列

##### 保证消息可靠性的做法

消息从发送端应用到接收端应用，中间有三个阶段需要保证可靠，分别是消息发送者把消息发送到消息中间件，消息中间件把消息存入消息存储，消息中间件把消息投递给消息消费者。

所以我们要保证这三个阶段都可靠，才能够保证最终消息的可靠。



### 做服务发现常用的框架

- zookeeper
- eureka
- etcd
- consul


### 分布式链路追踪使用过哪些

### 如何实现分布式锁

目前主要有如下三种方式：

基于数据库实现分布式锁； 
基于缓存（Redis等）实现分布式锁； 
基于Zookeeper实现分布式锁；

### 你使用过哪些负载策略知道的有哪些负载策略

随机或者带权重随机

轮询或者带权重轮询

一致性Hash

最小活跃数或者最快响应

### 分布式session存储方案

四个方案都是可行的，但是对于大型网站来说，Session Sticky和Session数据集中存储是比较好的方案。

《大型网站系统与[Java](http://lib.csdn.net/base/javaee)中间价实践》

#### Session Stick

 **Session Stick** 方案即将客户端的每次请求都转发至同一台服务器，这就需要负载均衡器能够根据每次请求的会话标识（SessionId）来进行请求转发 

这种方案实现比较简单，对于Web服务器来说和单机的情况一样。但是可能会带来如下问题：

1. 如果有一台服务器宕机或者重启，那么这台机器上的会话数据会全部丢失。

2. 会话标识是应用层信息，那么负载均衡要将同一个会话的请求都保存到同一个Web服务器上的话，就需要进行应用层（第7层）的解析，这个开销比第4层大。

3. 负载均衡器将变成一个有状态的节点，要将会话保存到具体Web服务器的映射。和无状态节点相比，内存消耗更大，容灾方面也会更麻烦。

#### Session Replication

Session Replication 的方案则不对负载均衡器做更改，而是在Web服务器之间增加了会话数据同步的功能，各个服务器之间通过同步保证不同Web服务器之间的Session数据的一致性

Session Replication 方案对负载均衡器不再有要求，但是同样会带来以下问题：

1. 同步Session数据会造成额外的网络带宽的开销，只要Session数据有变化，就需要将新产生的Session数据同步到其他服务器上，服务器数量越多，同步带来的网络带宽开销也就越大。

2. 每台Web服务器都需要保存全部的Session数据，如果整个集群的Session数量太多的话，则对于每台机器用于保存Session数据的占用会很严重。

#### Session 数据集中存储

Session 数据集中存储方案则是将集群中的所有Session集中存储起来，Web服务器本身则并不存储Session数据，不同的Web服务器从同样的地方来获取Session

相对于Session Replication方案，此方案的Session数据将不保存在本机，并且Web服务器之间也没有了Session数据的复制，但是该方案存在的问题在于：

1. 读写Session数据引入了网络操作，这相对于本机的数据读取来说，问题就在于存在时延和不稳定性，但是通信发生在内网，则问题不大。

2. 如果集中存储Session的机器或集群出现问题，则会影响应用。

#### Cookie Based

Cookie Based 方案是将Session数据放在Cookie里，访问Web服务器的时候，再由Web服务器生成对应的Session数据

但是Cookie Based 方案依然存在不足：

1. Cookie长度的限制。这会导致Session长度的限制。 

2. 安全性。Seesion数据本来是服务端数据，却被保存在了客户端，即使可以加密，但是依然存在不安全性。

3. 带宽消耗。这里不是指内部Web服务器之间的宽带消耗，而是数据中心的整体外部带宽的消耗。

4. 性能影响。每次HTTP请求和响应都带有Seesion数据，对Web服务器来说，在同样的处理情况下，响应的结果输出越少，支持的并发就会越高。

### 分布式事务了解吗？你们如何解决分布式事务问题的？

#### 3 XA方案也叫做两阶段提交事务方案.

举个例子，比如公司经常团建，然后一般会有个主席（就是负责组织团建的那个人）。

> tb，team building，团建

- 第一个阶段，一般tb主席会提前问团队里的每个人，说，大家伙，下周六我们去滑雪+烧烤，去吗？
   \- 这个时候tb主席开始等待每个人的回答，如果所有人都说ok，那么就可以决定一起去这次tb
   \- 如果这个阶段里，任何一个人回答说，我有事不去了，那么tb主席就会取消这次活动
- 第二个阶段，那下周六大家就一起去滑雪+烧烤了

这就是所谓的XA事务，两阶段提交

有一个事务管理器，负责协调多个数据库（资源管理器）的事务，事务管理器先问各个数据库你准备好了吗？

- 如果每个数据库都回复ok，那么就正式提交事务，在各个数据库上执行操作
- 任何一个数据库回答不ok，那么就回滚事务。

这种分布式事务方案，比较适合单块应用中，跨多个库的分布式事务，而且因为严重依赖于数据库层面来搞定复杂的事务，效率很低，绝对**不适合高并发**场景

如果要玩，那么基于Spring + JTA就可以搞定，自己随便搜个demo看看~

这个方案，很少用，一般**某个系统内部如果出现跨多个库**的操作，是不合规的!

现在的微服务，一个大的系统分成几十甚至上百个服务。一般来说，我们的规约，是要求每个服务只能操作自己对应的一个数据库!

如果你要操作别的服务对应的库，不允许直连别的服务的库，违反微服务架构的规范

你随便交叉访问，几百个服务的话，全体乱套，这样的一套服务是没法管理的，会经常数据被别人改错，自己的库被别人写挂!

如果你要操作别人的服务的库，你必须通过**调用别的服务的接口**实现，绝对不允许你交叉访问别人的数据库！

- 两阶段提交方案示意图
  ![img](https://ask.qcloudimg.com/http-save/1752328/zrjb53e0wh.png)

#### 4 TCC方案

全称:Try、Confirm、Cancel

##### 4.1 三个阶段

###### 4.1.1 Try

对各个服务的资源做检测,对资源进行锁定或者预留

###### 4.1.2 Confirm

在各个服务中执行实际的操作

###### 4.1.3 Cancel

如果任何一个服务的业务方法执行出错，那么这里就需要进行补偿，即执行已操作成功的业务逻辑的回滚操作

##### 4.2 跨银行转账案例

涉及到两个银行的分布式事务，如果用TCC方案来实现，思路是这样的：

- Try阶段
   先把两个银行账户中的资金给它冻结住,不让操作了
- Confirm阶段
   执行实际的转账操作，A银行账户的资金扣减，B银行账户的资金增加
- Cancel阶段
   如果任何一个银行的操作执行失败，那么就需要回滚进行补偿
   比如A银行账户如果已经扣减了，但是B银行账户资金增加失败了，那么就得把A银行账户资金给加回去

该方案说实话几乎很少使用，但也有使用场景.

因为这个事务的回滚实际上严重依赖于你自己写代码来回滚和补偿了，会造成补偿代码巨大，非常恶心!

比如说我们，一般来说和钱相关的支付、交易等相关的场景，我们会用TCC，严格严格保证分布式事务要么全部成功，要么全部自动回滚，严格保证资金的正确性!

##### 4.3 适用场景

除非你是真的一致性要求太高，是系统中核心之核心的场景!

常见的就是资金类的场景，那可以用TCC方案，自己编写大量的业务逻辑，自己判断一个事务中的各个环节是否ok，不ok就执行补偿/回滚代码

而且最好是你的各个业务执行的时间都比较短

但是说实话，一般尽量别这么搞，自己手写回滚逻辑，或者是补偿逻辑，实在太恶心了，业务代码也很难维护

- TCC方案
  ![img](https://ask.qcloudimg.com/http-save/1752328/zxtt97e47h.png)

#### 5 本地消息表

ebay搞出来的这么一套思想

##### 5.1 简介

- A系统在本地一个事务里操作的同时，插入一条数据到消息表
- 接着A系统将这个消息发送到MQ
- B系统接收到消息后，在一个事务里，往自己本地消息表里插入一条数据，同时执行其他的业务操作，如果这个消息已经被处理过了，那么此时这个事务会回滚，这样保证不会重复处理消息
- B系统执行成功后，就会更新自己本地消息表的状态以及A系统消息表的状态
- 如果B系统处理失败，那么就不会更新消息表状态，那么此时A系统会定时扫描自己的消息表，如果有未处理的消息，会再次发送到MQ中去，让B再处理

##### 5.2 优点

这个方案保证了**最终一致性**

哪怕B事务失败了，但是A会不断重发消息，直到B那边成功为止

##### 5.3 缺陷

最大的问题就在于严重依赖于数据库的消息表来管理事务,这个会导致高并发场景无力,难以扩展呢,一般确实很少用

- 本地消息表方案
  ![img](https://ask.qcloudimg.com/http-save/1752328/amebkemm0g.png)

#### 6 可靠消息最终一致性方案

干脆不用本地的消息表了，直接基于MQ来实现事务。比如阿里的RocketMQ就支持消息事务!

##### 6.1 简介

- A系统先发送一个prepared消息到MQ，如果这个prepared消息发送失败,那么就直接取消操作,不执行了
- 如果这个消息发送成功过了，那么接着执行本地事务，如果成功就告诉MQ发送确认消息，如果失败就告诉MQ回滚消息
- 如果发送了确认消息，那么此时B系统会接收到确认消息，然后执行本地的事务
- MQ会自动定时轮询所有prepared消息回调你的接口，问你这个消息是不是本地事务处理失败了，所有没发送确认的消息,是继续重试还是回滚？
   这里你就可以查下数据库看之前本地事务是否执行，如果回滚了，那么这里也回滚吧。这个就是避免可能本地事务执行成功了，别确认消息发送失败了。
- 如果系统B的事务失败了咋办？
   重试咯，自动不断重试直到成功，如果实在是不行，要么就是针对重要的资金类业务进行回滚，比如B系统本地回滚后，想办法通知系统A也回滚；或者是发送报警由人工来手工回滚和补偿

这个还是比较合适的，目前国内互联网公司大都是这么玩的，要不你举用RocketMQ支持的，要不你就自己基于类似ActiveMQ？RabbitMQ？自己封装一套类似的逻辑出来，总之思路就是这样子的



#### 7 最大努力通知方案

##### 7.1 简介

- 系统A本地事务执行完后，发送一个消息到MQ
- 有一专门消费MQ的最大努力通知服务，会消费MQ,然后写入数据库中记录下来，亦可是放入内存队列，接着调用系统B的接口
- 若系统B执行成功就ok；若系统B执行失败，那么最大努力通知服务就定时尝试重新调用系统B，反复N次，最后还是不行才放弃
- 最大努力通知方案示意图![img](https://ask.qcloudimg.com/http-save/1752328/hx700mt14x.png)




## 项目

### 开发中遇到的一些问题

#### Service 中一个A方法调用B方法，事务失效

因为在A方法调用的时候，是使用代理类 `ServiceProxy` 调用，先调用外层包装后，然后调用目标Service.A方法，但是在调用B方法的时候，已经进入了实际的Service，这个时候就直接调用Service.B方法

导致B方法的事务失效



#### Dubbo 调用接口返回为null

dubbo接口调用返回null,由于dubbo默认为同步调用，而如果在方法有async = true，则为异步。即时在方法上async = false，也不起作用。这个还是同dubbo的注入有一点关系，首先要看dubbo注入读取顺序，由于默认采用singleton="true"，所以如果第一个读取到是异步async = true，则所有注入的同这个实例相关都是异步。



## 开放问题

用一句话概括 Web 编程的特点

Google是如何在一秒内把搜索结果返回给用户

树（二叉或其他）形成许多普通数据结构的基础。请描述一些这样的数据结构以及何时可以使用它们

某一项功能如何设计

线上系统突然变得异常缓慢，你如何查找问题

什么样的项目不适合用框架

新浪微博是如何实现把微博推给订阅者

简要介绍下从浏览器输入 URL 开始到获取到请求界面之后 Java Web 应用中发生了什么

请你谈谈SSH整合

高并发下，如何做到安全的修改同一行数据

12306网站的订票系统如何实现，如何保证不会票不被超卖

网站性能优化如何优化的

聊了下曾经参与设计的服务器架构

请思考一个方案，实现分布式环境下的 countDownLatch

请思考一个方案，设计一个可以控制缓存总体大小的自动适应的本地缓存

在你的职业生涯中，算得上最困难的技术挑战是什么

编程中自己都怎么考虑一些设计原则的，比如开闭原则，以及在工作中的应用

解释一下网络应用的模式及其特点

设计一个在线文档系统，文档可以被编辑，如何防止多人同时对同一份文档进行编辑更新

说出数据连接池的工作机制是什么

怎么获取一个文件中单词出现的最高频率

如果有机会重新设计你们的产品，你会怎么做

如何搭建一个高可用系统

如何启动时不需输入用户名与密码

如何在基于Java的Web项目中实现文件上传和下载

如何实现一个秒杀系统，保证只有几位用户能买到某件商品。

如何实现负载均衡，有哪些算法可以实现

如何设计一个购物车？想想淘宝的购物车如何实现的

如何设计一套高并发支付方案，架构如何设计

### 如何设计建立和保持 100w 的长连接

服务器内核调优(tcp，文件数)，客户端调优，框架选择(netty)



如何避免浏览器缓存。

如何防止缓存雪崩

如果AB两个系统互相依赖，如何解除依

### 如果有人恶意创建非法连接，怎么解决

使用Filter，只是在不同的层面的，比如可以使用

如果有几十亿的白名单，每天白天需要高并发查询，晚上需要更新一次，如何设计这个功能

如果系统要使用超大整数（超过long长度范围），请你设计一个数据结构来存储这种超大型数字以及设计一种算法来实现超大整数加法运算）

如果让你实现一个并发安全的链表，你会怎么做

应用服务器与WEB 服务器的区别？应用服务器怎么监控性能，各种方式的区别？你使用过的应用服务器优化技术有哪些

大型网站在架构上应当考虑哪些问题

有没有处理过线上问题？出现内存泄露，CPU利用率标高，应用无响应时如何处理的

最近看什么书，印象最深刻的是什么

描述下常用的重构技巧

你使用什么版本管理工具？分支（Branch）与标签（Tag）之间的区别在哪里

你有了解过存在哪些反模式（Anti-Patterns）吗

你用过的网站前端优化的技术有哪些

如何分析Thread dump

你如何理解AOP中的连接点（Joinpoint）、切点（Pointcut）、增强（Advice）、引介（Introduction）、织入（Weaving）、切面（Aspect）这些概念

你是如何处理内存泄露或者栈溢出问题的

你们线上应用的 JVM 参数有哪些

怎么提升系统的QPS和吞吐量

## Web

### Http

#### 说说你知道的几种HTTP响应码

200：ok 一切正常

401：Unauthorized，登录权限的问题，要么错误，要么没有授权

404：not found



1xx：信息，请求收到，继续处理 
2xx：成功，行为被成功地接受、理解和采纳 
3xx：重定向，为了完成请求，必须进一步执行的动作 
4xx：客户端错误，请求包含语法错误或者请求无法实现 
5xx：服务器错误，服务器不能实现一种明显无效的请求

#### 如何避免浏览器缓存

无法被浏览器缓存的请求： 
HTTP信息头中包含Cache-Control:no-cache，pragma:no-cache，或Cache-Control:max-age=0等告诉浏览器不用缓存的请求
需要根据Cookie，认证信息等决定输入内容的动态请求是不能被缓存的 
经过HTTPS安全加密的请求（有人也经过测试发现，ie其实在头部加入Cache-Control：max-age信息，firefox在头部加入Cache-Control:Public之后，能够对HTTPS的资源进行缓存，参考《HTTPS的七个误解》）
POST请求无法被缓存 
HTTP响应头中不包含Last-Modified/Etag，也不包含Cache-Control/Expires的请求无法被缓存 
http://www.alloyteam.com/2012/03/web-cache-2-browser-cache/

### 聊聊HTTPS和SSL/TLS协议

####  SSL/TLS”是干嘛用滴 

 **SSL** 是洋文“Secure Sockets Layer”的缩写，中文叫做“**安全套接层**”。它是在上世纪90年代中期，由网景公司设计的。（顺便插一句，网景公司不光发明了 SSL，还发明了很多 Web 的基础设施——比如“CSS 样式表”和“JS 脚本”）
为啥要发明 SSL 这个协议捏？因为原先互联网上使用的 HTTP 协议是明文的，存在很多缺点——比如传输内容会被偷窥（嗅探）和篡改。发明 SSL 协议，就是为了解决这些问题。
到了1999年，SSL 因为应用广泛，已经成为互联网上的事实标准。**IETF 就在那年把 SSL 标准化**。标准化之后的名称改为 **TLS**（是“Transport Layer Security”的缩写），中文叫做“**传输层安全协议**”。
很多相关的文章都把这两者并列称呼（SSL/TLS），因为这两者可以视作同一个东西的不同阶段。 



####  HTTPS”是啥意思 

 解释完 HTTP 和 SSL/TLS，现在就可以来解释 HTTPS 啦。咱们通常所说的 HTTPS 协议，说白了就是“**HTTP  协议”和“SSL/TLS 协议”的组合**。你可以把 HTTPS 大致理解为——“HTTP over SSL”或“HTTP over TLS”（反正 SSL 和 TLS 差不多）。 

####  HTTP 和 TCP 之间的关系 

简单地说，TCP 协议是 HTTP 协议的基石——HTTP 协议需要依靠 TCP 协议来传输数据。

在网络分层模型中，TCP 被称为“传输层协议”，而 HTTP 被称为“应用层协议”。

有很多常见的应用层协议是以 TCP 为基础的，比如“FTP、SMTP、POP、IMAP”等。

#### HTTP 协议如何使用 TCP 连接？

HTTP 对 TCP 连接的使用，分为两种方式：俗称“短连接”和“长连接”（“长连接”又称“持久连接”，洋文叫做“Keep-Alive”或“Persistent Connection”）
假设有一个网页，里面包含好多图片，还包含好多【外部的】CSS 文件和 JS 文件。在“短连接”的模式下，浏览器会先发起一个 TCP 连接，拿到该网页的 HTML 源代码（拿到 HTML 之后，这个 TCP  连接就关闭了）。然后，浏览器开始分析这个网页的源码，知道这个页面包含很多外部资源（图片、CSS、JS）。然后针对【每一个】外部资源，再分别发起一个个 TCP 连接，把这些文件获取到本地（同样的，每抓取一个外部资源后，相应的 TCP 就断开）
相反，如果是“**长连接**”的方式，浏览器也会先发起一个 TCP 连接去抓取页面。但是抓取页面之后，该 TCP 连接并不会立即关闭，而是暂时先保持着（所谓的“Keep-Alive”）。然后浏览器分析  HTML 源码之后，发现有很多外部资源，就用刚才那个 TCP 连接去抓取此页面的外部资源。

在 HTTP 1.0 版本，【默认】使用的是“短连接”（那时候是 Web 诞生初期，网页相对简单，“短连接”的问题不大）；
到了1995年底开始制定 HTTP 1.1 草案的时候，网页已经开始变得复杂（网页内的图片、脚本越来越多了）。这时候再用短连接的方式，效率太低下了（因为建立 TCP  连接是有“时间成本”和“CPU 成本”滴）。所以，在 HTTP 1.1 中，【默认】采用的是“Keep-Alive”的方式。
关于“Keep-Alive”的更多介绍，可以参见维基百科词条

#### 啥是“对称加密”？

所谓的“对称加密技术”，意思就是说：“加密”和“解密”使用【相同的】密钥。这个比较好理解。就好比你用 7zip 或 WinRAR  创建一个带密码（口令）的加密压缩包。当你下次要把这个压缩文件解开的时候，你需要输入【同样的】密码。在这个例子中，密码/口令就如同刚才说的“密钥”。

#### 啥是“非对称加密”？

所谓的“非对称加密技术”，意思就是说：“加密”和“解密”使用【不同的】密钥。这玩意儿比较难理解，也比较难想到。当年“非对称加密”的发明，还被誉为“密码学”历史上的一次革命。
由于篇幅有限，对“非对称加密”这个话题，俺就不展开了。有空的话，再单独写一篇扫盲。

#### 对称加密 和 非对称加密 各自有啥优缺点？

看完刚才的定义，很显然：（从功能角度而言）“非对称加密”能干的事情比“对称加密”要多。这是“非对称加密”的优点。但是“非对称加密”的实现，通常需要涉及到“复杂数学问题”。所以，“非对称加密”的性能通常要差很多（相对于“对称加密”而言）。
这两者的优缺点，也影响到了 SSL 协议的设计。


