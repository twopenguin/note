---
typora-copy-images-to: picture
---

#Spring

### Spring注解

#### @Autowired

自动装配;

​	Spring利用依赖注入（DI），完成对IOC容器中中各个组件的依赖关系赋值；

一般用法如下：

```java
class BookService{
	@Autowired
	BookDao  bookDao;
}
```

使用规则和原理有下面几条： 

1）、默认优先按照类型去容器中找对应的组件:applicationContext.getBean(BookDao.class);找到就赋值
2）、如果找到多个相同类型的组件，再将属性的名称作为组件的id去容器中查找

```java
applicationContext.getBean("bookDao")
```
3）、@Qualifier("bookDao")：使用@Qualifier指定需要装配的组件的id，而不是使用属性名
4）、自动装配默认一定要将属性赋值好，没有就会报错；
```java
可以使用@Autowired(required=false);
```
5）、@Primary：让Spring进行自动装配的时候，默认使用首选的bean；也可以继续使用@Qualifier指定需要装配的bean的名字

**原理：**

**AutowiredAnnotationBeanPostProcessor:解析完成自动装配功能；**

 3）、 @Autowired:**可以注解在构造器，参数，方法，属性**；都是从容器中获取参数组件的值
 		1）、[标注在方法位置]：@Bean+方法参数；参数从容器中获取;默认不写@Autowired效果是一样的；都能自动装配
 		2）、[标在构造器上]：如果组件只有一个有参构造器，这个有参构造器的@Autowired可以省略，参数位置的组件还是可以自动从容器中获取
 		3）、放在参数位置：

####@Bean

`@Configuration` 注解注释的类中的方法加上`@Bean` 注解就是注册一个Bean，如下：

```java
//配置类==配置文件
@Configuration  //告诉Spring这是一个配置类
public class MainConfig {
	
	//给容器中注册一个Bean;类型为返回值的类型，id默认是用方法名作为id
	@Bean("person")
	public Person person01(){
		return new Person("lisi", 20);
	}
}
```

##### 指定初始化和销毁方法

bean的生命周期：

 * 		bean创建---初始化----销毁的过程
    * 	容器管理bean的生命周期；
       * 我们可以自定义初始化和销毁方法；容器在bean进行到当前生命周期的时候来调用我们自定义的初始化和销毁方法

######方法一

我们可以使用`@Bean` 来这个定义：

```java
public class MainConfigOfLifeCycle {
	
	@Bean(initMethod="init",destroyMethod="destory")
	public Car car(){
		return new Car();
	}
}
```

来看看`Car` 这个对象，

```java
@Component
public class Car {
	
	public Car(){
		System.out.println("car constructor...");
	}
	
	public void init(){
		System.out.println("car ... init...");
	}
	
	public void destory(){
		System.out.println("car ... detory...");
	}

}
```

`Car` 中的 `init` 和 `destory` 方法对应上面的 `initMethod="init",destroyMethod="destory"`

#### @ComponentScan

```java
//配置类==配置文件
@Configuration  //告诉Spring这是一个配置类

@ComponentScans(
		value = {
				@ComponentScan(value="com.atguigu",includeFilters = {
/*						@Filter(type=FilterType.ANNOTATION,classes={Controller.class}),
						@Filter(type=FilterType.ASSIGNABLE_TYPE,classes={BookService.class}),*/
						@Filter(type=FilterType.CUSTOM,classes={MyTypeFilter.class})
				},useDefaultFilters = false)	
		}
		)
//@ComponentScan  value:指定要扫描的包
//excludeFilters = Filter[] ：指定扫描的时候按照什么规则排除那些组件
//includeFilters = Filter[] ：指定扫描的时候只需要包含哪些组件
//FilterType.ANNOTATION：按照注解
//FilterType.ASSIGNABLE_TYPE：按照给定的类型；
//FilterType.ASPECTJ：使用ASPECTJ表达式
//FilterType.REGEX：使用正则指定
//FilterType.CUSTOM：使用自定义规则
public class MainConfig {
```

**自定义过滤规则**

对于上面代码中提到了一种过滤规则是`FilterType.CUSTOM` ,那我们怎么自定义一个过滤规则呢，看源码注释入下：

```java
/** Filter candidates using a given custom
 * {@link org.springframework.core.type.filter.TypeFilter} implementation.
 */
CUSTOM
```

通过注释，它告诉我们我们要实现`TypeFilter` 接口，如下：

```java
public class MyTypeFilter implements TypeFilter {

	/**
	 * metadataReader：读取到的当前正在扫描的类的信息
	 * metadataReaderFactory:可以获取到其他任何类信息的
	 */
	@Override
	public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory)
			throws IOException {
		// TODO Auto-generated method stub
		//获取当前类注解的信息
		AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
		//获取当前正在扫描的类的类信息
		ClassMetadata classMetadata = metadataReader.getClassMetadata();
		//获取当前类资源（类的路径）
		Resource resource = metadataReader.getResource();
		
		String className = classMetadata.getClassName();
		System.out.println("--->"+className);
		if(className.contains("er")){
			return true;
		}
		return false;
	}
}
```

由上面代码我们就可以看出我们可以根据传入的参数来获取当前正在扫描的类的**注解、类型、路径**等信息，然后供我们来决定怎么过滤

#### @Conditional

按照一定的条件进行判断，满足条件给容器中注册bean，这个注解可以**使用在类上面，或者方法上面**。

使用在方法上面：

```java
/**
 * @Conditional({Condition}) ： 按照一定的条件进行判断，满足条件给容器中注册bean
 * 
 * 如果系统是windows，给容器中注册("bill")
 * 如果是linux系统，给容器中注册("linus")
 */

@Conditional(LinuxCondition.class)
@Bean("bill")
public Person person01(){
  return new Person("Bill Gates",62);
}

@Conditional(LinuxCondition.class)
@Bean("linus")
public Person person02(){
  return new Person("linus", 48);
}
```

` @Conditional` 这个注解需要传入实现了`Condition` 接口的类

```java
public @interface Conditional {

	/**
	 * All {@link Condition}s that must {@linkplain Condition#matches match}
	 * in order for the component to be registered.
	 */
	Class<? extends Condition>[] value();
}
```

这儿我用两个类实现了这个接口：

```java
//判断是否linux系统
public class LinuxCondition implements Condition {

	/**
	 * ConditionContext：判断条件能使用的上下文（环境）
	 * AnnotatedTypeMetadata：注释信息
	 */
	@Override
	public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
		// TODO是否linux系统
		//1、能获取到ioc使用的beanfactory
		ConfigurableListableBeanFactory beanFactory = context.getBeanFactory();
		//2、获取类加载器
		ClassLoader classLoader = context.getClassLoader();
		//3、获取当前环境信息
		Environment environment = context.getEnvironment();
		//4、获取到bean定义的注册类
		BeanDefinitionRegistry registry = context.getRegistry();
		
		String property = environment.getProperty("os.name");
		
		//可以判断容器中的bean注册情况，也可以给容器中注册bean
		boolean definition = registry.containsBeanDefinition("person");
		if(property.contains("linux")){
			return true;
		}
		return false;
	}
}
```

```java
//判断是否windows系统
public class WindowsCondition implements Condition {

	@Override
	public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
		Environment environment = context.getEnvironment();
		String property = environment.getProperty("os.name");
		if(property.contains("Windows")){
			return true;
		}
		return false;
	}
}

```

使用在类上面：

```java
//类中组件统一设置。满足当前条件，这个类中配置的所有bean注册才能生效；
@Conditional({WindowsCondition.class})
@Configuration
public class MainConfig2 {
}
```







#### @Configuration

告诉Spring这是一个配置类，就像以前使用xml配置时候的一个Bean配置文件，如下：

```java
//配置类==配置文件
@Configuration  //告诉Spring这是一个配置类
public class MainConfig {
}
```

下面是创建一个注解Spring上下文，来获取Bean的测试

```java
ApplicationContext applicationContext = new AnnotationConfigApplicationContext(MainConfig.class);
Person bean = applicationContext.getBean(Person.class);
System.out.println(bean);

String[] namesForType = applicationContext.getBeanNamesForType(Person.class);
for (String name : namesForType) {
	System.out.println(name);
}
```

#### @EnableAspectJAutoProxy

开启基于注解的aop模式:

具体可以参看Spring Aop部分

#### @Import(容器中注册组件)

给容器中注册组件有下面的四种方式：

```java
/**
 * 给容器中注册组件；
 * 1）、包扫描+组件标注注解（@Controller/@Service/@Repository/@Component）[自己写的类]
 * 2）、@Bean[导入的第三方包里面的组件]
 * 3）、@Import[快速给容器中导入一个组件]
 *    1）、@Import(要导入到容器中的组件)；容器中就会自动注册这个组件，id默认是全类名
 *    2）、ImportSelector:返回需要导入的组件的全类名数组；
 *    3）、ImportBeanDefinitionRegistrar:手动注册bean到容器中
 * 4）、使用Spring提供的 FactoryBean（工厂Bean）;
 *    1）、默认获取到的是工厂bean调用getObject创建的对象
 *    2）、要获取工厂Bean本身，我们需要给id前面加一个&
 *      &colorFactoryBean
 */
```

上面的3-1，就是说的是使用`@Import` 注解导入组件的方式，下面来看一个示例

```java
@Import({Color.class,Red.class})
//@Import导入组件，id默认是组件的全类名
public class MainConfig2 {
}
```

Color 类代码：

```java
public class Color {
	private Car car;
	public Car getCar() {
		return car;
	}
	public void setCar(Car car) {
		this.car = car;
	}
	@Override
	public String toString() {
		return "Color [car=" + car + "]";
	}
}
```

##### ImportSelector

对于`@Import` 注解不仅能直接传类似上面`Color` 类这样的单纯的Bean，还可以传入`ImportSelector` 接口的实现类，我们看看

`@Import` 注解的注释：

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Import {

	/**
	 * {@link Configuration}, {@link ImportSelector}, {@link ImportBeanDefinitionRegistrar}
	 * or regular component classes to import.
	 */
	Class<?>[] value();

}
```

这儿有一个`ImportSelector` 的实现类：

```java
//自定义逻辑返回需要导入的组件
public class MyImportSelector implements ImportSelector {

	//返回值，就是到导入到容器中的组件全类名
	//AnnotationMetadata:当前标注@Import注解的类的所有注解信息
	@Override
	public String[] selectImports(AnnotationMetadata importingClassMetadata) {
		// TODO Auto-generated method stub
		//importingClassMetadata
		//方法不要返回null值
		return new String[]{"com.atguigu.bean.Blue","com.atguigu.bean.Yellow"};
	}
}
```

这个接口的`selectImports` 方法返回的组件的全类名数组，都是被注册进容器

##### ImportBeanDefinitionRegistrar

由上面还可以看出，`@Import` 注解还可以传入`ImportBeanDefinitionRegistrar` 接口的实现类，我们来看看这个接口：

```java
public interface ImportBeanDefinitionRegistrar {

	/**
	 * Register bean definitions as necessary based on the given annotation metadata of
	 * the importing {@code @Configuration} class.
	 * <p>Note that {@link BeanDefinitionRegistryPostProcessor} types may <em>not</em> be
	 * registered here, due to lifecycle constraints related to {@code @Configuration}
	 * class processing.
	 * @param importingClassMetadata annotation metadata of the importing class
	 * @param registry current bean definition registry
	 */
	public void registerBeanDefinitions(
			AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry);

}
```

看到这个接口的唯一方法`registerBeanDefinitions` 的名字，我们就感觉这个接口可以让我们自己注册`BeanDefinition`

下面自定义个一个实现

```java
public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {

	/**
	 * AnnotationMetadata：当前类的注解信息
	 * BeanDefinitionRegistry:BeanDefinition注册类；
	 * 		把所有需要添加到容器中的bean；调用
	 * 		BeanDefinitionRegistry.registerBeanDefinition手工注册进来
	 */
	@Override
	public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
		
		boolean definition = registry.containsBeanDefinition("com.atguigu.bean.Red");
		boolean definition2 = registry.containsBeanDefinition("com.atguigu.bean.Blue");
		if(definition && definition2){
			//指定Bean定义信息；（Bean的类型，Bean。。。）
			RootBeanDefinition beanDefinition = new RootBeanDefinition(RainBow.class);
			//注册一个Bean，指定bean名
			registry.registerBeanDefinition("rainBow", beanDefinition);
		}
	}

}

```

##### FactoryBean

这儿之所以把`FactoryBean` 放在这里，是因为我们也可以通过`FactoryBean` 来注册组件，

`FactoryBean` 是一个接口，定义如下：

```java
public interface FactoryBean<T> {
	T getObject() throws Exception;
	Class<?> getObjectType();
	boolean isSingleton();
}
```

这儿有个类实现了这个接口：

```java
//创建一个Spring定义的FactoryBean
public class ColorFactoryBean implements FactoryBean<Color> {

	//返回一个Color对象，这个对象会添加到容器中
	@Override
	public Color getObject() throws Exception {
		// TODO Auto-generated method stub
		System.out.println("ColorFactoryBean...getObject...");
		return new Color();
	}

	@Override
	public Class<?> getObjectType() {
		// TODO Auto-generated method stub
		return Color.class;
	}

	//是单例？
	//true：这个bean是单实例，在容器中保存一份
	//false：多实例，每次获取都会创建一个新的bean；
	@Override
	public boolean isSingleton() {
		// TODO Auto-generated method stub
		return false;
	}

}
```

我们把这个类`ColorFactoryBean` 注册进入容器中：

```java
 /*
 * 4）、使用Spring提供的 FactoryBean（工厂Bean）;
 *       1）、默认获取到的是工厂bean调用getObject创建的对象
 *       2）、要获取工厂Bean本身，我们需要给id前面加一个&
 *          &colorFactoryBean
 */
@Bean
public ColorFactoryBean colorFactoryBean(){
   return new ColorFactoryBean();
}
```

基于之前我们在 `@Bean`  这一节提到的，我们可以根据`colorFactoryBean` 这个id 从容器中获取到Bean，但是默认获取到的Bean的类型是`Color` 而不是 `ColorFactoryBean` ，规则如下：

 *    1）、默认获取到的是工厂bean调用getObject创建的对象
 *    2）、要获取工厂Bean本身，我们需要给id前面加一个&

      &colorFactoryBean





####@Lazy

这个注解用于懒加载，懒加载只针对于**单例的Bean**

```java
/*
 * 懒加载：
 *    单实例bean：默认在容器启动的时候创建对象；
 *    懒加载：容器启动不创建对象。第一次使用(获取)Bean创建对象，并初始化；
 */
@Lazy
@Bean("person")
public Person person(){
  System.out.println("给容器中添加Person....");
  return new Person("张三", 25);
}
```

因为单例的Bean默认的`Scope` 是单例，然而单例的会在容器启动的时候创建对象，我们加上`@Lazy` 这个注解就会让**此对象不在容器创建的时候被创建**

#### @PostConstruct  (JSR250)

1.这个注解是只能放在方法上面的：并且一般和`@PreDestroy`一起配对使用

```java
@Documented
@Retention (RUNTIME)
@Target(METHOD)
public @interface PostConstruct {
}
```

看一个实例来看看怎么使用这个注解：

```java
@Component
public class Dog{	
	public Dog(){
		System.out.println("dog constructor...");
	}
	
	//对象创建并赋值之后调用
	@PostConstruct
	public void init(){
		System.out.println("Dog....@PostConstruct...");
	}
	
	//容器移除对象之前
	@PreDestroy
	public void detory(){
		System.out.println("Dog....@PreDestroy...");
	}
}
```

#### @PreDestroy  (JSR250)

参考`@PostConstruct` 

#### @Profile

  Profile：
  		Spring为我们提供的可以根据当前环境，动态的激活和切换一系列组件的功能；

  开发环境、测试环境、生产环境；
  数据源：(/A)(/B)(/C)；


  @Profile：指定组件在哪个环境的情况下才能被注册到容器中，不指定，任何环境下都能注册这个组件

  1）、加了环境标识的bean，只有这个环境被激活的时候才能注册到容器中。默认是default环境
  2）、写在配置类上，只有是指定的环境的时候，整个配置类里面的所有配置才能开始生效
  3）、没有标注环境标识的bean在，任何环境下都是加载的；



接下来看看一个实例：

```java
@PropertySource("classpath:/dbconfig.properties")
@Configuration
public class MainConfigOfProfile implements EmbeddedValueResolverAware{
	
	@Value("${db.user}")
	private String user;
	
	private StringValueResolver valueResolver;
	
	private String  driverClass;
	
	
	@Bean
	public Yellow yellow(){
		return new Yellow();
	}
	
	@Profile("test")
	@Bean("testDataSource")
	public DataSource dataSourceTest(@Value("${db.password}")String pwd) throws Exception{
		ComboPooledDataSource dataSource = new ComboPooledDataSource();
		dataSource.setUser(user);
		dataSource.setPassword(pwd);
		dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
		dataSource.setDriverClass(driverClass);
		return dataSource;
	}
	
	
	@Profile("dev")
	@Bean("devDataSource")
	public DataSource dataSourceDev(@Value("${db.password}")String pwd) throws Exception{
		ComboPooledDataSource dataSource = new ComboPooledDataSource();
		dataSource.setUser(user);
		dataSource.setPassword(pwd);
		dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/ssm_crud");
		dataSource.setDriverClass(driverClass);
		return dataSource;
	}
	
	@Profile("prod")
	@Bean("prodDataSource")
	public DataSource dataSourceProd(@Value("${db.password}")String pwd) throws Exception{
		ComboPooledDataSource dataSource = new ComboPooledDataSource();
		dataSource.setUser(user);
		dataSource.setPassword(pwd);
		dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/scw_0515");
		
		dataSource.setDriverClass(driverClass);
		return dataSource;
	}

	@Override
	public void setEmbeddedValueResolver(StringValueResolver resolver) {
		// TODO Auto-generated method stub
		this.valueResolver = resolver;
		driverClass = valueResolver.resolveStringValue("${db.driverClass}");
	}

}
```

##### 激活某个Profile

第一种方式：加vm参数 -Dspring.profiles.active=xxx

第二种方式：使用无参的Spring上下文

```java
AnnotationConfigApplicationContext configApplicationContext = new AnnotationConfigApplicationContext();
configApplicationContext.getEnvironment().setActiveProfiles("XXX");
configApplicationContext.register(MyConfig.class);
configApplicationContext.refresh();
```

这个步骤相对于Spring 上下文的源码就是**多了一个设置环境**，可以看看下面对比一下：

```java
public AnnotationConfigApplicationContext(Class<?>... annotatedClasses) {
    this();
    register(annotatedClasses);
    refresh();
}
```



#### @PropertySource

用来制定`Properties` 文件的位置

在以前我们在bean.xml 配置文件配置 properties 配置文件我们是这么做的：

```xml
<context:property-placeholder location="classpath:person.properties"/>
```

现在我们可以这么做：

```java
//使用@PropertySource读取外部配置文件中的k/v保存到运行的环境变量中;加载完外部的配置文件以后使用${}取出配置文件的值
@PropertySource(value={"classpath:/person.properties"})
@Configuration
public class MainConfigOfPropertyValues {
	
	@Bean
	public Person person(){
		return new Person();
	}
}
```



#### @Scope

此注解用来调整作用域，即单例或者原型等等

可以有四个取值：默认是 singleton

singleton、prototype、request、sesssion

 prototype：多实例的：ioc容器启动并不会去调用方法创建对象放在容器中。每次获取的时候才会调用方法创建对象；

singleton：单实例的（默认值）：ioc容器启动会调用方法创建对象放到ioc容器中。以后每次获取就是直接从容器（map.get()）中拿，

```java
//默认是单实例的
/**
 * ConfigurableBeanFactory#SCOPE_PROTOTYPE    
 * @see ConfigurableBeanFactory#SCOPE_SINGLETON  
 * @see org.springframework.web.context.WebApplicationContext#SCOPE_REQUEST  request
 * @see org.springframework.web.context.WebApplicationContext#SCOPE_SESSION  sesssion
 * @return\
 * @Scope:调整作用域
 * prototype：多实例的：ioc容器启动并不会去调用方法创建对象放在容器中。
 *          每次获取的时候才会调用方法创建对象；
 * singleton：单实例的（默认值）：ioc容器启动会调用方法创建对象放到ioc容器中。
 *      以后每次获取就是直接从容器（map.get()）中拿，
 * request：同一次请求创建一个实例
 * session：同一个session创建一个实例
 */
@Scope("prototype")
@Bean("person")
public Person person(){
  System.out.println("给容器中添加Person....");
  return new Person("张三", 25);
}
```

#### @Value

这个注解可用来属性赋值，用法大概如下：

```java
public class Person {
	
	//使用@Value赋值；
	//1、基本数值
	//2、可以写SpEL； #{}
	//3、可以写${}；取出配置文件【properties】中的值（在运行环境变量里面的值）
	
	@Value("张三")
	private String name;
}
```



### Spring接口

####BeanPostProcessor

BeanPostProcessor【interface】：bean的后置处理器；**初始化前后**

在bean初始化前后进行一些处理工作；怎么来理解这个初始化，先看看`BeanPostProcessor` 中`postProcessBeforeInitialization` 方法的部分注释

```java
   /**
	* Apply this BeanPostProcessor to the given new bean instance <i>before</i> any bean
	* initialization callbacks (like InitializingBean's {@code afterPropertiesSet}
	* or a custom init-method). The bean will already be populated with property values.
	*/
	Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;
```

说的是，这个方法会在，`InitializingBean` 接口的 `afterPropertiesSet` 方法和 我们自定义的 init-method 的方法之前调用，让我们来看看一个实例，下面是一个Bean 实现了`InitializingBean, DisposableBean` 接口，

```java
/**
 * Created by zcd on 2018/3/14.
 */
public class Dog implements InitializingBean, DisposableBean{

    public Dog(){
        System.out.println("Dog's Constructor ....");
    }

    public void init(){
        System.out.println("Dog init");
    }

    public void myDestroy(){
        System.out.println("Dog myDestroy");
    }

    public void afterPropertiesSet() throws Exception {
        System.out.println("Dog afterPropertiesSet");
    }

    public void destroy() throws Exception {
        System.out.println("Dog destroy");
    }
}
```

在这儿注册进入容器：

```java
@Configuration
public class MyConfig {

    @Bean(initMethod = "init", destroyMethod = "myDestroy")
    public Dog dog(){
        return new Dog();
    }
}
```

来看一下运行结果：

```
Dog's Constructor ....
postProcessBeforeInitialization -> beanName=dog  bean,s Classcom.atguigu.bean.Dog
Dog afterPropertiesSet
Dog init
postProcessAfterInitialization -> beanName=dog  bean,s Classcom.atguigu.bean.Dog

Dog destroy
Dog myDestroy
```

得出初步结论：

优先级

postProcessBeforeInitialization 》afterPropertiesSet 》自定义 init方法

来看看一段源代码(来自Spring `AbstractAutowireCapableBeanFactory` 的`initializeBean` 方法)来说明我们刚刚上面得出的结论：

```java
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
```

 BeanPostProcessor原理
 populateBean(beanName, mbd, instanceWrapper);给bean进行属性赋值
 initializeBean
 {
 applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);
 invokeInitMethods(beanName, wrappedBean, mbd);执行自定义初始化
 applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);
 }

在Spring 中有个方法先给bean 的属性赋值`populateBean` ,然后进入 `initializeBean` 方法，然后调用顺序就如同上面所所说的

##### BeanPostProcessor在Spring底层运用

 Spring底层对 BeanPostProcessor 的使用；

      bean赋值，注入其他组件，@Autowired，生命周期注解功能，@Async,xxx BeanPostProcessor;

###### ApplicationContextAwareProcessor

我们自己定义的Bean可以实现 `ApplicationContextAware` 这个接口来让这个Bean感知到Spring 容器，如下：

```java
@Component
public class Dog implements ApplicationContextAware {
	
	//@Autowired
	private ApplicationContext applicationContext;
	
	public Dog(){
		System.out.println("dog constructor...");
	}

	@Override
	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		// TODO Auto-generated method stub
		this.applicationContext = applicationContext;
	}

}
```

这个`ApplicationContextAware` 的实现原理就是`ApplicationContextAwareProcessor` ,来看看这个类：

```java
class ApplicationContextAwareProcessor implements BeanPostProcessor {

	private final ConfigurableApplicationContext applicationContext;

	private final StringValueResolver embeddedValueResolver;

	@Override
	public Object postProcessBeforeInitialization(final Object bean, String beanName) throws BeansException {
		AccessControlContext acc = null;

		if (System.getSecurityManager() != null &&
				(bean instanceof EnvironmentAware || bean instanceof EmbeddedValueResolverAware ||
						bean instanceof ResourceLoaderAware || bean instanceof ApplicationEventPublisherAware ||
						bean instanceof MessageSourceAware || bean instanceof ApplicationContextAware)) {
			acc = this.applicationContext.getBeanFactory().getAccessControlContext();
		}

		if (acc != null) {
			AccessController.doPrivileged(new PrivilegedAction<Object>() {
				@Override
				public Object run() {
					invokeAwareInterfaces(bean);
					return null;
				}
			}, acc);
		}
		else {
			invokeAwareInterfaces(bean);
		}

		return bean;
	}

	private void invokeAwareInterfaces(Object bean) {
		if (bean instanceof Aware) {
			if (bean instanceof EnvironmentAware) {
				((EnvironmentAware) bean).setEnvironment(this.applicationContext.getEnvironment());
			}
			if (bean instanceof EmbeddedValueResolverAware) {
				((EmbeddedValueResolverAware) bean).setEmbeddedValueResolver(this.embeddedValueResolver);
			}
			if (bean instanceof ResourceLoaderAware) {
				((ResourceLoaderAware) bean).setResourceLoader(this.applicationContext);
			}
			if (bean instanceof ApplicationEventPublisherAware) {
				((ApplicationEventPublisherAware) bean).setApplicationEventPublisher(this.applicationContext);
			}
			if (bean instanceof MessageSourceAware) {
				((MessageSourceAware) bean).setMessageSource(this.applicationContext);
			}
			if (bean instanceof ApplicationContextAware) {
				((ApplicationContextAware) bean).setApplicationContext(this.applicationContext);
			}
		}
	}

	@Override
	public Object postProcessAfterInitialization(Object bean, String beanName) {
		return bean;
	}

}
```

这个类`ApplicationContextAwareProcessor` 实现了`BeanPostProcessor` 接口，而且在`postProcessBeforeInitialization` 会检查我们的Bean 是否实现了`ApplicationContextAware` 接口，然后在`invokeAwareInterfaces` 方法中调用`setApplicationContext` 方法

###### BeanValidationPostProcessor

可以用来对Bean做验证

```java
public class BeanValidationPostProcessor implements BeanPostProcessor, InitializingBean {
	@Override
	public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		if (!this.afterInitialization) {
			doValidate(bean);
		}
		return bean;
	}
}
```

###### InitDestroyAnnotationBeanPostProcessor

用来处理JSR250的 @PostConstruct 注解和 @PreDestroy 注解

```java
public class InitDestroyAnnotationBeanPostProcessor
		implements DestructionAwareBeanPostProcessor, MergedBeanDefinitionPostProcessor, PriorityOrdered, Serializable 
{
	/**
	 * Specify the init annotation to check for, indicating initialization
	 * methods to call after configuration of a bean.
	 * <p>Any custom annotation can be used, since there are no required
	 * annotation attributes. There is no default, although a typical choice
	 * is the JSR-250 {@link javax.annotation.PostConstruct} annotation.
	 */
	public void setInitAnnotationType(Class<? extends Annotation> initAnnotationType) {
		this.initAnnotationType = initAnnotationType;
	}
	
		@Override
	public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
	     //先获取类注解信息
		LifecycleMetadata metadata = findLifecycleMetadata(bean.getClass());
		try {
		    //反射调用
			metadata.invokeInitMethods(bean, beanName);
		}
		catch (InvocationTargetException ex) {
			throw new BeanCreationException(beanName, "Invocation of init method failed", ex.getTargetException());
		}
		catch (Throwable ex) {
			throw new BeanCreationException(beanName, "Failed to invoke init method", ex);
		}
		return bean;
	}
}
		
```

###### AutowiredAnnotationBeanPostProcessor

这个是用来处理`@Autowired` 注解和 JSR-330 的`@Inject` 注解的实现原理的



#### InitializingBean & DisposableBean 

我们还可以让我们自己的Bean 实现`InitializingBean`  和 `DisposableBean` 接口，**从而让Bean可以做一些初始化和销毁工作**，分别对应于`@bean` 中配置的 `initMethod` 和

`destroyMethod` ,我们来看一个实例

```java
@Component
public class Cat implements InitializingBean,DisposableBean {
	
	public Cat(){
		System.out.println("cat constructor...");
	}

	@Override
	public void destroy() throws Exception {
		// TODO Auto-generated method stub
		System.out.println("cat...destroy...");
	}

	@Override
	public void afterPropertiesSet() throws Exception {
		// TODO Auto-generated method stub
		System.out.println("cat...afterPropertiesSet...");
	}
}
```

这样在容器注册这个Bean的时候，就会在Bean创建好，并且属性赋值好之后调用，`afterPropertiesSet` 方法，在容器关闭的时候调用`destroy` 方法，有一点要注意，就是有如下的规则；

- **销毁：**
  - **单实例：容器关闭的时候会调用`destroy` 方法**
  - **多实例：容器不会管理这个bean；容器不会调用销毁方法；**



#### 





### Spring问题整理

#### 指定Bean的初始化和销毁方法



**1. **通过@Bean指定init-method和destroy-method；

**2. **通过让Bean实现InitializingBean（定义初始化逻辑），DisposableBean（定义销毁逻辑）;

**3.** 可以使用JSR250；

@PostConstruct：在bean创建完成并且属性赋值完成；来执行初始化方法

@PreDestroy：在容器销毁bean之前通知我们进行清理工作

**4. **BeanPostProcessor【interface】：bean的后置处理器；
* 	在bean初始化前后进行一些处理工作；
   * postProcessBeforeInitialization:在初始化之前工作
    * postProcessAfterInitialization:在初始化之后工作


#### 自动装配

1）、就是使用`@Autowired` 这个可以参考上面注解中

2）、Spring还支持使用@Resource(JSR250)和@Inject(JSR330)[java规范的注解]

	@Resource:
		可以和@Autowired一样实现自动装配功能；默认是按照组件名称进行装配的；
		没有能支持@Primary功能没有支持@Autowired（reqiured=false）;
	@Inject:
		需要导入javax.inject的包，和Autowired的功能一样。没有required=false的功能；
@Autowired:Spring定义的； @Resource、@Inject都是java规范

**ps：使用`@Inject` 注解还要导入其他的依赖：**

```xml
<!-- https://mvnrepository.com/artifact/javax.inject/javax.inject -->
<dependency>
    <groupId>javax.inject</groupId>
    <artifactId>javax.inject</artifactId>
    <version>1</version>
</dependency>

```

#### Aware注入Spring底层组件

`Aware` 接口是Spring的一个标志型接口

 自定义组件想要使用Spring容器底层的一些组件（ApplicationContext，BeanFactory，xxx）；
 		自定义组件实现xxxAware；在创建对象的时候，会调用接口规定的方法注入相关组件；Aware；
  		把Spring底层一些组件注入到自定义的Bean中；

实现原理：一个xxxAware 都对应着，一个xxxProcessor 来实现，而这个xxxProcessor 就是一个后置处理器；

比如：ApplicationContextAware==》ApplicationContextAwareProcessor；

其实很多的`Aware` 的处理都是在`ApplicationContextAwareProcessor` 中的`invokeAwareInterfaces` 处理的

```java
private void invokeAwareInterfaces(Object bean) {
    if (bean instanceof Aware) {
        if (bean instanceof EnvironmentAware) {
            ((EnvironmentAware) bean).setEnvironment(this.applicationContext.getEnvironment());
        }
        if (bean instanceof EmbeddedValueResolverAware) {
            ((EmbeddedValueResolverAware) bean).setEmbeddedValueResolver(this.embeddedValueResolver);
        }
        if (bean instanceof ResourceLoaderAware) {
            ((ResourceLoaderAware) bean).setResourceLoader(this.applicationContext);
        }
        if (bean instanceof ApplicationEventPublisherAware) {
            ((ApplicationEventPublisherAware) bean).setApplicationEventPublisher(this.applicationContext);
        }
        if (bean instanceof MessageSourceAware) {
            ((MessageSourceAware) bean).setMessageSource(this.applicationContext);
        }
        if (bean instanceof ApplicationContextAware) {
            ((ApplicationContextAware) bean).setApplicationContext(this.applicationContext);
        }
    }
}
```

#### 使用Properties配置中的值

有下面几种方式：

第一种：属性上使用`@Value` 注解

```java
@Value("${db.user}")
private String user;
```

第二种：参数上使用`@Value` 注解

```java
	@Bean("testDataSource")
	public DataSource dataSourceTest(@Value("${db.password}")String pwd) throws Exception{
		//TODO
	}
```

第三种：使用`EmbeddedValueResolverAware` 接口，自己注入进来String解析器，来自己解析

```java
	@Override
	public void setEmbeddedValueResolver(StringValueResolver resolver) {
		// TODO Auto-generated method stub
		this.valueResolver = resolver;
		driverClass = valueResolver.resolveStringValue("${db.driverClass}");
	}
```

具体的代码可以参考下面：

```java
@PropertySource("classpath:/dbconfig.properties")
@Configuration
public class MainConfigOfProfile implements EmbeddedValueResolverAware{
	
	@Value("${db.user}")
	private String user;
	
	private StringValueResolver valueResolver;
	
	private String  driverClass;
	
	
	@Bean
	public Yellow yellow(){
		return new Yellow();
	}
	
	@Profile("test")
	@Bean("testDataSource")
	public DataSource dataSourceTest(@Value("${db.password}")String pwd) throws Exception{
		ComboPooledDataSource dataSource = new ComboPooledDataSource();
		dataSource.setUser(user);
		dataSource.setPassword(pwd);
		dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
		dataSource.setDriverClass(driverClass);
		return dataSource;
	}

	@Override
	public void setEmbeddedValueResolver(StringValueResolver resolver) {
		// TODO Auto-generated method stub
		this.valueResolver = resolver;
		driverClass = valueResolver.resolveStringValue("${db.driverClass}");
	}

}
```

### Spring Aop

 AOP：【动态代理】
 		指在程序运行期间动态的将某段代码切入到指定方法指定位置进行运行的编程方式；

#### 基本玩法

 1、导入aop模块；Spring AOP：(spring-aspects)

```xml
<!-- https://mvnrepository.com/artifact/org.springframework/spring-aspects -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-aspects</artifactId>
    <version>4.3.12.RELEASE</version>
</dependency>

```

 2、定义一个业务逻辑类（MathCalculator）；在业务逻辑运行的时候将日志进行打印（方法之前、方法运行结束、方法出现异常，xxx）

```java
public class MathCalculator {
	
	public int div(int i,int j){
		System.out.println("MathCalculator...div...");
		return i/j;	
	}
}
```

 3、定义一个日志切面类（LogAspects）：切面类里面的方法需要动态感知MathCalculator.div运行到哪里然后执行；
 		通知方法：
 			前置通知(@Before)：logStart：在目标方法(div)运行之前运行
 			后置通知(@After)：logEnd：在目标方法(div)运行结束之后运行（无论方法正常结束还是异常结束）
 			返回通知(@AfterReturning)：logReturn：在目标方法(div)正常返回之后运行
 			异常通知(@AfterThrowing)：logException：在目标方法(div)出现异常以后运行
 			环绕通知(@Around)：动态代理，手动推进目标方法运行（joinPoint.procced()）

```java
/**
 * 切面类
 * @author lfy
 * 
 * @Aspect： 告诉Spring当前类是一个切面类
 *
 */
@Aspect
public class LogAspects {
	
	//抽取公共的切入点表达式
	//1、本类引用
	//2、其他的切面引用
	@Pointcut("execution(public int com.atguigu.aop.MathCalculator.*(..))")
	public void pointCut(){};
	
	//@Before在目标方法之前切入；切入点表达式（指定在哪个方法切入）
	@Before("pointCut()")
	public void logStart(JoinPoint joinPoint){
		Object[] args = joinPoint.getArgs();
		System.out.println(""+joinPoint.getSignature().getName()+"运行。。。@Before:参数列表是：{"+Arrays.asList(args)+"}");
	}
	
	@After("com.atguigu.aop.LogAspects.pointCut()")
	public void logEnd(JoinPoint joinPoint){
		System.out.println(""+joinPoint.getSignature().getName()+"结束。。。@After");
	}
	
	//JoinPoint一定要出现在参数表的第一位
	@AfterReturning(value="pointCut()",returning="result")
	public void logReturn(JoinPoint joinPoint,Object result){
		System.out.println(""+joinPoint.getSignature().getName()+"正常返回。。。@AfterReturning:运行结果：{"+result+"}");
	}
	
	@AfterThrowing(value="pointCut()",throwing="exception")
	public void logException(JoinPoint joinPoint,Exception exception){
		System.out.println(""+joinPoint.getSignature().getName()+"异常。。。异常信息：{"+exception+"}");
	}

}
```

 4、给切面类的目标方法标注何时何地运行（通知注解）；

截取上面类中的某些代码

```java
	//抽取公共的切入点表达式
	//1、本类引用
	//2、其他的切面引用
	@Pointcut("execution(public int com.atguigu.aop.MathCalculator.*(..))")
	public void pointCut(){};
```

 5、将切面类和业务逻辑类（目标方法所在类）都加入到容器中;

```java
@Configuration
public class MainConfigOfAOP {
	 
	//业务逻辑类加入容器中
	@Bean
	public MathCalculator calculator(){
		return new MathCalculator();
	}

	//切面类加入到容器中
	@Bean
	public LogAspects logAspects(){
		return new LogAspects();
	}
}
```

 6、必须告诉Spring哪个类是切面类(给切面类上加一个注解：@Aspect)

```java
@Aspect
public class LogAspects {
}
```

 7、给配置类中加 @EnableAspectJAutoProxy 【开启基于注解的aop模式】
 		在Spring中很多的 @EnableXXX;

```java
@EnableAspectJAutoProxy
@Configuration
public class MainConfigOfAOP {
}
```

#### Aop 注解原理

因为需要加` @EnableAspectJAutoProxy ` 注解才能开启基于注解的aop模式，那我们打开这个注解看看

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Import(AspectJAutoProxyRegistrar.class)
public @interface EnableAspectJAutoProxy {
	boolean proxyTargetClass() default false;
	boolean exposeProxy() default false;
}
```

因为我们在使用`@EnableAspectJAutoProxy` 注解的时候没有使用属性，所以我们暂时不用管这个注解的两个属性

**1.**我们发现这个注解中使用的一个注解如下：

```java
@Import(AspectJAutoProxyRegistrar.class)
```

**2.**我们来看看这个类`AspectJAutoProxyRegistrar` :

```java
class AspectJAutoProxyRegistrar implements ImportBeanDefinitionRegistrar {

	/**
	 * Register, escalate, and configure the AspectJ auto proxy creator based on the value
	 * of the @{@link EnableAspectJAutoProxy#proxyTargetClass()} attribute on the importing
	 * {@code @Configuration} class.
	 */
	@Override
	public void registerBeanDefinitions(
			AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {

		AopConfigUtils.registerAspectJAnnotationAutoProxyCreatorIfNecessary(registry);

		AnnotationAttributes enableAspectJAutoProxy =
				AnnotationConfigUtils.attributesFor(importingClassMetadata, EnableAspectJAutoProxy.class);
		if (enableAspectJAutoProxy.getBoolean("proxyTargetClass")) {
			AopConfigUtils.forceAutoProxyCreatorToUseClassProxying(registry);
		}
		if (enableAspectJAutoProxy.getBoolean("exposeProxy")) {
			AopConfigUtils.forceAutoProxyCreatorToExposeProxy(registry);
		}
	}

}
```

我们之前接触过`@Import` 这个注解，知道这个注解可以可以传入`ImportBeanDefinitionRegistrar` 的实现类

（ps：抛出一个问题，见猜想1） 

**3.**我们看看`registerBeanDefinitions` 方法中的这么一段代码：

```java
AopConfigUtils.registerAspectJAnnotationAutoProxyCreatorIfNecessary(registry);
```

这段代码先不进去看，先知道结果，这段代码就是向Spring容器中加一个类型是`AnnotationAwareAspectJAutoProxyCreator.class`的一个Bean

**4.**打开这个类`AnnotationAwareAspectJAutoProxyCreator`，发现有点复杂，看看继承关系

![](../picture/TIM图片20180320205704.png)

我们发现这个类是`BeanPostProcessor` 和`BeanFactoryAware` 这两个接口的实现类，而这两个接口我们比较熟悉，可以从这儿入手

5.

### Spring猜想和验证

#### 1，ImportBeanDefinitionRegistrar 的实现类是否会加入Spring容器

具体描述：

我们之前接触过`@Import` 这个注解，知道这个注解可以可以传入`ImportBeanDefinitionRegistrar` 的实现类

Spring容器中是否有这个实现类，还是只有`registerBeanDefinitions` 这个方法中加入`BeanDefinitionRegistry` 注册器中的Bean才在Spring容器中。

答案是：**Spring容器中没有这个`ImportBeanDefinitionRegistrar` 接口的实现类**

代码如下：

`ImportBeanDefinitionRegistrar` 接口实现类：

```java
public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {

    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {

        RootBeanDefinition definition = new RootBeanDefinition(Dog.class);
        registry.registerBeanDefinition("dog", definition);
    }
}
```

配饰类：

```java
@Import(MyImportBeanDefinitionRegistrar.class)
@Configuration
public class MyConfig {

}
```

测试类：

```java
public class IOCTest {

    @Test
    public void teatPostBeanProcessor(){
        final AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(MyConfig.class);
        for (String s : applicationContext.getBeanDefinitionNames()) {
            System.out.println("Bean Name:"+s);
        }
        // Object object = applicationContext.getBean(MyImportBeanDefinitionRegistrar.class);
        applicationContext.close();
    }
}
```

结果展示：

```
Bean Name:org.springframework.context.annotation.internalConfigurationAnnotationProcessor
Bean Name:org.springframework.context.annotation.internalAutowiredAnnotationProcessor
Bean Name:org.springframework.context.annotation.internalRequiredAnnotationProcessor
Bean Name:org.springframework.context.annotation.internalCommonAnnotationProcessor
Bean Name:org.springframework.context.event.internalEventListenerProcessor
Bean Name:org.springframework.context.event.internalEventListenerFactory
Bean Name:myConfig
Bean Name:myPostProcessor
Bean Name:springDao
Bean Name:springService
Bean Name:dog
```

结果显示：只有`Dog` 加入了容器，`MyImportBeanDefinitionRegistrar` 没有加入容器

## Template

在Spring 中有 很多的 Template 比如：`JdbcTemplate` ，`RedisTemplate`， `RestTemplate`.

他们的使用方法也是大同小异的；这儿暂时不写使用的方法，而是借助`JdbcTemplate` 源码来理解其中的思想以及代理和回调的妙处。

从 `JdbcTemplate` 的 `execute` 方法开始：

```java

```



# SpringMVC

## SpringHelloWorld

鉴于以后开发都是使用idea开发，所以开发步骤如下：

1. 创建idea web项目
2. 导包



### 创建 idea web项目

在创建project的和其他的项目一样，只是在new module的时候像下面这样：

![1526267528026](../picture/1526267528026.png)



#### 配置Tomcat服务器

1. 在下面红色框中配置的 On ‘Update’ action ，选择Update classes and resources 是为了热部署
2. 在下面的After launch 我们看见一个chrome的图标，这儿的配置是为了在Tomcat启动的时候自动的打开该浏览器

![1526268245487](../picture/1526268245487.png)



#### 部署一个web应用

1. 在tomcat的配置页面选择 Deployment ，然后有个绿色的+来部署一个web应用，（ps：建议选择exploded 结尾的模式，只有这种模式才能热部署，解释如下）

```
是选择war还是war exploded 这里首先看一下他们两个的区别：
war模式：将WEB工程以包的形式上传到服务器 ；
war exploded模式：将WEB工程以当前文件夹的位置关系上传到服务器；1212

（1）war模式这种可以称之为是发布模式，看名字也知道，这是先打成war包，再发布；
（2）war exploded模式是直接把文件夹、jsp页面 、classes等等移到Tomcat 部署文件夹里面，进行加载部署。因此这种方式支持热部署，一般在开发的时候也是用这种方式。
（3）在平时开发的时候，使用热部署的话，应该对Tomcat进行相应的设置，这样的话修改的jsp界面什么的东西才可以及时的显示出来。
```

2. 在下面图片中的Application Context 的那个地方，如果填写的 / 那么就会覆盖tomcat 默认的根目录`ROOT` 所以，建议修改

![1526272645046](../picture/1526272645046.png)

### 代码编写

#### 配置 `web.xml`

```xml
<!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" >

<web-app>
  <display-name>Archetype Created Web Application</display-name>

  <servlet>
    <servlet-name>dispactorServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>classpath:dispactorServlet.xml</param-value>
    </init-param>
  </servlet>
  
  <servlet-mapping>
    <servlet-name>dispactorServlet</servlet-name>
    <url-pattern>/</url-pattern>
  </servlet-mapping>
  
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
  </welcome-file-list>
</web-app>
```

#### Spring Bean XML 配置

```xml
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

    <context:component-scan base-package="com.zcd.spring.springmvc" />

    <!-- 配置视图解析器: 如何把 handler 方法返回值解析为实际的物理视图 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/views/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean>
</beans>  
```

#### Handler 编写

```java
@Controller
@RequestMapping("/hello")
public class Handler {

    @RequestMapping("/say")
    public String sayHello(){
        System.out.println("hello world");
        return "success";
    }
}
```

##入

### 使用 @RequestMapping 映射请求 

Spring MVC 使用 @RequestMapping 注解为控制器指定可以处理哪些 URL 请求 .

####映射URL

在控制器的类定义及方法定义处都可标注 

```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Mapping
public @interface RequestMapping {
}
```

1. 类定义处：提供初步的请求映射信息。相对于 WEB 应用的根目录
2. 方法处：提供进一步的细分映射信息。相对于类定义处的 URL。若类定义处未标注 @RequestMapping，则方法处标记的 URL 相对于WEB 应用的根目录 

DispatcherServlet 截获请求后，就通过控制器上@RequestMapping 提供的映射信息确定请求所对应的处理方法。 

此实例可以参看前面的`SpringHelloWorld`章节

#### 映射请求参数、请求方法或请求头 

@RequestMapping 除了可以使用请求 URL 映射请求外，还可以使用请求方法、请求参数及请求头映射请求 

@RequestMapping 的 value、method、params 及 heads分别表示<u>请求 URL、请求方法、请求参数及请求头</u>的映射条件，他们之间是 <u>**==与==**</u> 的关系，联合使用多个条件可让请求映射更加精确化。 

```java
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Mapping
public @interface RequestMapping {
    String[] value() default {};

    RequestMethod[] method() default {};

    String[] params() default {};

    String[] headers() default {};

    String[] consumes() default {};

    String[] produces() default {};
}
```

params 和 headers支持简单的表达式：
– param1: 表示请求必须包含名为 param1 的请求参数
– !param1: 表示请求不能包含名为 param1 的请求参数
– param1 != value1: 表示请求包含名为 param1 的请求参数，但其值
不能为 value1
– {“param1=value1”, “param2”}: 请求必须包含名为 param1 和param2
的两个请求参数，且 param1 参数的值必须为 value1 

#### Ant 风格的 URL 

@RequestMapping 还支持 Ant 风格的 URL：

Ant 风格资源地址支持 3 种匹配符：
– ?：匹配文件名中的一个字符
– *：匹配文件名中的任意字符
– **：** 匹配多层路径 


– /user/*/createUser: 匹配
/user/aaa/createUser、/user/bbb/createUser 等 URL
– /user/**/createUser: 匹配
/user/createUser、/user/aaa/bbb/createUser 等 URL
– /user/createUser??: 匹配
/user/createUseraa、/user/createUserbb 等 URL 

### @PathVariable 映射 URL 绑定的占位符 

带占位符的 URL 是 Spring3.0 新增的功能，该功能在SpringMVC 向 REST 目标挺进发展过程中具有里程碑的意义 

通过 @PathVariable 可以将 URL 中占位符参数绑定到控制器处理方法的入参中：URL 中的 {xxx} 占位符可以通过
@PathVariable("xxx") 绑定到操作方法的入参中。 



实例如下：

```java
/**
	 * @PathVariable 可以来映射 URL 中的占位符到目标方法的参数中.
	 * @param id
	 * @return
	 */
@RequestMapping("/testPathVariable/{id}")
public String testPathVariable(@PathVariable("id") Integer id) {
  System.out.println("testPathVariable: " + id);
  return SUCCESS;
}
```

请求方式：

```html
<a href="springmvc/testPathVariable/1">Test PathVariable</a>
<br><br>
```

### 请求处理方法签名

1. Spring MVC 通过分析处理方法的签名，将 **HTTP **请求信息绑定到处理方法的相应人参中。
2. Spring MVC 对控制器处理方法签名的限制是很宽松的，几乎可以按喜欢的任何方式对方法进行签名。
3. 必要时可以对方法及方法入参标注相应的注解（**@PathVariable**、**@RequestParam**、**@RequestHeader **等）、Spring
4. MVC 框架会将 HTTP 请求的信息绑定到相应的方法入参中，并根据方法的返回值类型做出相应的后续处理。

#### 使用 @RequestParam 绑定请求参数值

在处理方法入参处使用 **@RequestParam **可以把请求参数传递给请求方法.

1. value：参数名
2. required：是否必须。默认为 true, 表示请求参数中必须包含对应的参数，若不存在，将抛出异常

看看`@RequestParam` 的源码

```java
@Target(ElementType.PARAMETER)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface RequestParam {

	String value() default "";
	boolean required() default true;
	String defaultValue() default ValueConstants.DEFAULT_NONE;

}
```

实例：

```java
	/**
	 * @RequestParam 来映射请求参数. value 值即请求参数的参数名 required 该参数是否必须. 默认为 true
	 *               defaultValue 请求参数的默认值
	 */
	@RequestMapping(value = "/testRequestParam")
	public String testRequestParam(
			@RequestParam(value = "username") String un,
			@RequestParam(value = "age", required = false, defaultValue = "0") int age) {
		System.out.println("testRequestParam, username: " + un + ", age: "
				+ age);
		return SUCCESS;
	}
```

#### 使用 @RequestHeader 绑定请求报头的属性值

请求头包含了若干个属性，服务器可据此获知客户端的信息，通过 **@RequestHeader **即可将请求头中的属性值绑
定到处理方法的入参中.

实例：

```java
	/**
	 * 了解: 映射请求头信息 用法同 @RequestParam
	 */
	@RequestMapping("/testRequestHeader")
	public String testRequestHeader(
			@RequestHeader(value = "Accept-Language") String al) {
		System.out.println("testRequestHeader, Accept-Language: " + al);
		return SUCCESS;
	}
```

#### 使用 @CookieValue 绑定请求中的 Cookie 值

**@CookieValue **可让处理方法入参绑定某个 Cookie 值

实例：

```java
	/**
	 * 了解:
	 * 
	 * @CookieValue: 映射一个 Cookie 值. 属性同 @RequestParam
	 */
	@RequestMapping("/testCookieValue")
	public String testCookieValue(@CookieValue("JSESSIONID") String sessionId) {
		System.out.println("testCookieValue: sessionId: " + sessionId);
		return SUCCESS;
	}
```

### 使用 POJO 对象绑定请求参数值

Spring MVC 会按请求参数名和 **POJO **属性名进行自动匹配，自动为该对象填充属性值。支持级联属性。如：dept.deptId、dept.address.tel 等。

实例：

```java
/**
	 * Spring MVC 会按请求参数名和 POJO 属性名进行自动匹配， 自动为该对象填充属性值。支持级联属性。
	 * 如：dept.deptId、dept.address.tel 等
	 */
@RequestMapping("/testPojo")
public String testPojo(User user) {
  System.out.println("testPojo: " + user);
  return SUCCESS;
}

public class User {
	
	private Integer id;
	
	private String username;
	private String password;

	private String email;
	private int age;
}

public class Address {

	private String province;
	private String city;
}
```

请求HTML：

```html
<form action="springmvc/testPojo" method="post">
	username: <input type="text" name="username"/>
	<br>
	password: <input type="password" name="password"/>
	<br>
	email: <input type="text" name="email"/>
	<br>
	age: <input type="text" name="age"/>
	<br>
	city: <input type="text" name="address.city"/>
	<br>
	province: <input type="text" name="address.province"/>
	<br>
	<input type="submit" value="Submit"/>
</form>
<br><br>
```

### 使用 **Servlet API **作为入参

可以接受的ServletAPI 类型的参数

```
HttpServletRequest
HttpServletResponse
HttpSession
java.security.Principal
Locale
InputStream
OutputStream
Reader
Writer
```

实例：

```java
	/**
	 * 可以使用 Serlvet 原生的 API 作为目标方法的参数 具体支持以下类型
	 * 
	 * HttpServletRequest 
	 * HttpServletResponse 
	 * HttpSession
	 * java.security.Principal 
	 * Locale 
	 * InputStream 
	 * OutputStream 
	 * Reader 
	 * Writer
	 * @throws IOException 
	 */
	@RequestMapping("/testServletAPI")
	public void testServletAPI(HttpServletRequest request,
			HttpServletResponse response, Writer out) throws IOException {
		System.out.println("testServletAPI, " + request + ", " + response);
		out.write("hello springmvc");
//		return SUCCESS;
	}
```

## 出

Spring MVC 提供了以下几种途径输出模型数据：

1. ModelAndView：处理方法返回值类型为 `ModelAndView`时, 方法体即可通过该对象添加模型数据
2. **Map **及 **Model**: 入参为org.springframework.ui.Model、org.springframework.ui.ModelMap 或 java.uti.Map 时，处理方法返回时，Map中的数据会自动添加到模型中。
3. **@SessionAttributes**: 将模型中的某个属性暂存到HttpSession 中，以便多个请求之间可以共享这个属性
4. **@ModelAttribute**: 方法入参标注该注解后, 入参的对象就会放到数据模型中



### ModelAndView

控制器处理方法的返回值如果为 **ModelAndView**, **则其既包含视图信息，也包含模型数据信息**。

添加模型数据:
– MoelAndView addObject(String attributeName, Object attributeValue)
– ModelAndView addAllObject(Map<String, ?> modelMap)

设置视图:
– void setView(View view)
– void setViewName(String viewName)

实例：

```java
	/**
	 * 目标方法的返回值可以是 ModelAndView 类型。 
	 * 其中可以包含视图和模型信息
	 * SpringMVC 会把 ModelAndView 的 model 中数据放入到 request 域对象中. 
	 * @return
	 */
	@RequestMapping("/testModelAndView")
	public ModelAndView testModelAndView(){
		String viewName = SUCCESS;
		ModelAndView modelAndView = new ModelAndView(viewName);
		
		//添加模型数据到 ModelAndView 中.
		modelAndView.addObject("time", new Date());
		
		return modelAndView;
	}
```

### Map 及 Model

Spring MVC 在内部使用了一个`org.springframework.ui.Model` 接口存储模型数据。

1. **Spring MVC **在调用方法前会创建一个隐含的模型对象作为模型数据的存储容器。
2. 如果方法的入参为 **Map **或 **Model **类型，Spring MVC 会将隐含模型的引用传递给这些入参。在方法体内，开发者可以通过这个入参对象访问到模型中的所有数据，也可以向模型中添加新的属性数据。

实例：

```java
	/**
	 * 目标方法可以添加 Map 类型(实际上也可以是 Model 类型或 ModelMap 类型)的参数. 
	 * @param map
	 * @return
	 */
	@RequestMapping("/testMap")
	public String testMap(Map<String, Object> map){
		System.out.println(map.getClass().getName()); 
		map.put("names", Arrays.asList("Tom", "Jerry", "Mike"));
		return SUCCESS;
	}
```

### @SessionAttributes

若希望在多个请求之间共用某个模型属性数据，则可以在控制器类上标注一个 **@SessionAttributes**, Spring MVC将在模型中对应的属性暂存到 HttpSession 中。
• @SessionAttributes 除了可以通过属性名指定需要放到会话中的属性外，还可以通过模型属性的对象类型指定哪些模型属性需要放到会话中

常见用法：

– @SessionAttributes(types=User.class) 会将隐含模型中所有类型为 User.class 的属性添加到会话中。
– @SessionAttributes(value={“user1”, “user2”})
– @SessionAttributes(types={User.class, Dept.class})
– @SessionAttributes(value={“user1”, “user2”},types={Dept.class})

### @ModelAttribute

#### 场景描述：

我们要修改数据库中一条数据，但是对于密码和录入时间这些字段是不会修改的，但是我们在数据库的操作的时候传入的一般是一个model，这个model的密码和录入时间在我们没有传值的时候就是null值，所以在做数据库修改的时候就会把他们设置为null值，这明显是不对的，

而我们就可以使用 @ModelAttribute 注解来解决这个问题，加了注解的方法会在我们的目标方法执行之前先去让我们先从数据库查询一个原始的Model，这样我们就能避免上面的那个不合理的情况了。

#### 代码操作

```html
	<!--  
		模拟修改操作
		1. 原始数据为: 1, Tom, 123456,tom@atguigu.com,12
		2. 密码不能被修改.
		3. 表单回显, 模拟操作直接在表单填写对应的属性值
	-->
	<form action="springmvc/testModelAttribute" method="Post">
		<input type="hidden" name="id" value="1"/>
		username: <input type="text" name="username" value="Tom"/>
		<br>
		email: <input type="text" name="email" value="tom@atguigu.com"/>
		<br>
		age: <input type="text" name="age" value="12"/>
		<br>
		<input type="submit" value="Submit"/>
	</form>
	<br><br>
```



```java
	/**
	 * 1. 有 @ModelAttribute 标记的方法, 会在每个目标方法执行之前被 SpringMVC 调用! 
	 * 2. @ModelAttribute 注解也可以来修饰目标方法 POJO 类型的入参, 其 value 属性值有如下的作用:
	 * 1). SpringMVC 会使用 value 属性值在 implicitModel 中查找对应的对象, 若存在则会直接传入到目标方法的入参中.
	 * 2). SpringMVC 会一 value 为 key, POJO 类型的对象为 value, 存入到 request 中. 
	 */
	@ModelAttribute
	public void getUser(@RequestParam(value="id",required=false) Integer id, 
			Map<String, Object> map){
		System.out.println("modelAttribute method");
		if(id != null){
			//模拟从数据库中获取对象
			User user = new User(1, "Tom", "123456", "tom@atguigu.com", 12);
			System.out.println("从数据库中获取一个对象: " + user);
			
			map.put("user", user);
		}
	}
	
    @RequestMapping("/testModelAttribute")
    public String testModelAttribute(User user){
        System.out.println("修改: " + user);
        return "success";
    }
```

#### 原理描述：

运行流程:

1. 执行 @ModelAttribute 注解修饰的方法: 从数据库中取出对象, 把对象放入到了 Map 中. 键为: user
2. SpringMVC 从 Map 中取出 User 对象, 并把表单的请求参数赋给该 User 对象的对应属性.
3. SpringMVC 把上述对象传入目标方法的参数.



SpringMVC 确定目标方法 POJO 类型入参的过程：

1.  确定一个 key:

```
1). 若目标方法(testModelAttribute)的 POJO 类型的参数木有使用 @ModelAttribute(此注解可以用来修饰方法和参数) 作为修饰, 则 key 为 POJO 类名第一个字母的小写
2). 若使用了  @ModelAttribute 来修饰, 则 key 为 @ModelAttribute 注解的 value 属性值.
```

2. 在 implicitModel 中查找 key 对应的对象, 若存在, 则作为入参传入

```
1). 若在 @ModelAttribute 标记的方法中在 Map 中保存过, 且 key 和 1 确定的 key 一致, 则会获取到.
```

3. 若 implicitModel 中不存在 key 对应的对象, 则检查当前的 Handler 是否使用 @SessionAttributes 注解修饰, 若使用了该注解, 且 @SessionAttributes 注解的 value 属性值中包含了 key, 则会从 HttpSession 中来获取 key 所对应的 value 值, 若存在则直接传入到目标方法的入参中. 若不存在则将抛出异常. 
4. 若 Handler 没有标识 @SessionAttributes 注解或 @SessionAttributes 注解的 value 值中不包含 key, 则会通过反射来创建 POJO 类型的参数, 传入为目标方法的参数
5. SpringMVC 会把 key 和 POJO 类型的对象保存到 implicitModel 中, 进而会保存到 request 中. 

 **注意: 在 @ModelAttribute 修饰的方法中, 放入到 Map 时的键需要和目标方法入参类型的第一个字母小写的字符串一致!**



```java
	/**
	 * 
	 * 注意: 在 @ModelAttribute 修饰的方法中, 放入到 Map 时的键需要和目标方法入参类型的第一个字母小写的字符串一致!

	 * 源代码分析的流程
	 * 1. 调用 @ModelAttribute 注解修饰的方法. 实际上把 @ModelAttribute 方法中 Map 中的数据放在了 implicitModel 中.
	 * 2. 解析请求处理器的目标参数, 实际上该目标参数来自于 WebDataBinder 对象的 target 属性
	 * 1). 创建 WebDataBinder 对象:
	 * ①. 确定 objectName 属性: 若传入的 attrName 属性值为 "", 则 objectName 为类名第一个字母小写. 
	 * *注意: attrName. 若目标方法的 POJO 属性使用了 @ModelAttribute 来修饰, 则 attrName 值即为 @ModelAttribute 
	 * 的 value 属性值 
	 * 
	 * ②. 确定 target 属性:
	 * 	> 在 implicitModel 中查找 attrName 对应的属性值. 若存在, ok
	 * 	> *若不存在: 则验证当前 Handler 是否使用了 @SessionAttributes 进行修饰, 若使用了, 则尝试从 Session 中
	 * 获取 attrName 所对应的属性值. 若 session 中没有对应的属性值, 则抛出了异常. 
	 * 	> 若 Handler 没有使用 @SessionAttributes 进行修饰, 或 @SessionAttributes 中没有使用 value 值指定的 key
	 * 和 attrName 相匹配, 则通过反射创建了 POJO 对象
	 * 
	 * 2). SpringMVC 把表单的请求参数赋给了 WebDataBinder 的 target 对应的属性. 
	 * 3). *SpringMVC 会把 WebDataBinder 的 attrName 和 target 给到 implicitModel. 
	 * 近而传到 request 域对象中. 
	 * 4). 把 WebDataBinder 的 target 作为参数传递给目标方法的入参. 
	 */
```

原理：

```java
public final Object invokeHandlerMethod(Method handlerMethod, Object handler,
		NativeWebRequest webRequest, ExtendedModelMap implicitModel) throws Exception {

	Method handlerMethodToInvoke = BridgeMethodResolver.findBridgedMethod(handlerMethod);
	try {
		boolean debug = logger.isDebugEnabled();
		for (String attrName : this.methodResolver.getActualSessionAttributeNames()) {
			Object attrValue = this.sessionAttributeStore.retrieveAttribute(webRequest, attrName);
			if (attrValue != null) {
				implicitModel.addAttribute(attrName, attrValue);
			}
		}
         //遍历所有实现了@ModelAttributes 注解的方法，先调用
		for (Method attributeMethod : this.methodResolver.getModelAttributeMethods()) {
			Method attributeMethodToInvoke = BridgeMethodResolver.findBridgedMethod(attributeMethod);
			Object[] args = resolveHandlerArguments(attributeMethodToInvoke, handler, webRequest, implicitModel);
			if (debug) {
				logger.debug("Invoking model attribute method: " + attributeMethodToInvoke);
			}
			String attrName = AnnotationUtils.findAnnotation(attributeMethod, ModelAttribute.class).value();
			if (!"".equals(attrName) && implicitModel.containsAttribute(attrName)) {
				continue;
			}
			ReflectionUtils.makeAccessible(attributeMethodToInvoke);
			Object attrValue = attributeMethodToInvoke.invoke(handler, args);
			if ("".equals(attrName)) {
				Class<?> resolvedType = GenericTypeResolver.resolveReturnType(attributeMethodToInvoke, handler.getClass());
				attrName = Conventions.getVariableNameForReturnType(attributeMethodToInvoke, resolvedType, attrValue);
			}
			if (!implicitModel.containsAttribute(attrName)) {
				implicitModel.addAttribute(attrName, attrValue);
			}
		}
		Object[] args = resolveHandlerArguments(handlerMethodToInvoke, handler, webRequest, implicitModel);
		if (debug) {
			logger.debug("Invoking request handler method: " + handlerMethodToInvoke);
		}
		ReflectionUtils.makeAccessible(handlerMethodToInvoke);
		return handlerMethodToInvoke.invoke(handler, args);
	}
	catch (IllegalStateException ex) {
		// Internal assertion failed (e.g. invalid signature):
		// throw exception with full handler method context...
		throw new HandlerMethodInvocationException(handlerMethodToInvoke, ex);
	}
	catch (InvocationTargetException ex) {
		// User-defined @ModelAttribute/@InitBinder/@RequestMapping method threw an exception...
		ReflectionUtils.rethrowException(ex.getTargetException());
		return null;
	}
}
```

### 视图和视图解析器

无论目标方法返回的是`String`、`Map`、`ModelAndView`、`View` 最后返回的都是ModelAndView

## SpringMVC 流程

我们直接从`DispacterServlet`的`doDispatch`方法开始，下面就是这个方法的源代码：

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
	HttpServletRequest processedRequest = request;
	HandlerExecutionChain mappedHandler = null;
	boolean multipartRequestParsed = false;

	WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

	try {
		ModelAndView mv = null;
		Exception dispatchException = null;

		try {
			processedRequest = checkMultipart(request);
			multipartRequestParsed = processedRequest != request;

			// Determine handler for the current request.
              //① 根据 HttpServletRequest  获取 HandlerExecutionChain对象
			mappedHandler = getHandler(processedRequest);
			if (mappedHandler == null || mappedHandler.getHandler() == null) {
				noHandlerFound(processedRequest, response);
				return;
			}

			// Determine handler adapter for the current request.
			HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());

			// Process last-modified header, if supported by the handler.
			String method = request.getMethod();
			boolean isGet = "GET".equals(method);
			if (isGet || "HEAD".equals(method)) {
				long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
				if (logger.isDebugEnabled()) {
					String requestUri = urlPathHelper.getRequestUri(request);
					logger.debug("Last-Modified value for [" + requestUri + "] is: " + lastModified);
				}
				if (new ServletWebRequest(request, response).checkNotModified(lastModified) && isGet) {
					return;
				}
			}

			if (!mappedHandler.applyPreHandle(processedRequest, response)) {
				return;
			}

			try {
				// Actually invoke the handler.
				mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
			}
			finally {
				if (asyncManager.isConcurrentHandlingStarted()) {
					return;
				}
			}

			applyDefaultViewName(request, mv);
			mappedHandler.applyPostHandle(processedRequest, response, mv);
		}
		catch (Exception ex) {
			dispatchException = ex;
		}
		processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
	}
	catch (Exception ex) {
		triggerAfterCompletion(processedRequest, response, mappedHandler, ex);
	}
	catch (Error err) {
		triggerAfterCompletionWithError(processedRequest, response, mappedHandler, err);
	}
	finally {
		if (asyncManager.isConcurrentHandlingStarted()) {
			// Instead of postHandle and afterCompletion
			mappedHandler.applyAfterConcurrentHandlingStarted(processedRequest, response);
			return;
		}
		// Clean up any resources used by a multipart request.
		if (multipartRequestParsed) {
			cleanupMultipart(processedRequest);
		}
	}
}
```

### 1.根据`HttpServletRequest`  获取 `HandlerExecutionChain`对象

从`doDispactch`的下面这句代码开始：

```java
mappedHandler = getHandler(processedRequest);
```

点开这个方法看看：

```java
protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
	for (HandlerMapping hm : this.handlerMappings) {
		if (logger.isTraceEnabled()) {
			logger.trace(
					"Testing handler map [" + hm + "] in DispatcherServlet with name '" + getServletName() + "'");
		}
		HandlerExecutionChain handler = hm.getHandler(request);
		if (handler != null) {
			return handler;
		}
	}
	return null;
}
```

上面这个方法还是是`DispacterServlet`的，`this.handlerMappings`就是其内部的一个属性定义，看看这个属性的定义：

```java
/** List of HandlerMappings used by this servlet */
private List<HandlerMapping> handlerMappings;
```



### 2.获取`HandlerAdapter`

```java
// Determine handler adapter for the current request.
HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
```

打开这个方法看看

![1526973163672](../picture/1526973163672.png)

在`DispatcherServlet` 的`this.handlerAdapters`的定义如下：

```java
/** List of HandlerAdapters used by this servlet */
private List<HandlerAdapter> handlerAdapters;
```

这个属性在运行是有三个值，如上面截图所示：



# Spring MVC原理

## URL对应的处理类存放在哪里：

![1526974323802](../picture/1526974323802.png)



## DispatcherServlet

Spring MVC 核心 Servlet （DispatcherServlet）架构图如下：

![1527228374459](../picture/1527228374459.png)



这里主要讲 Spring 中的 HttpServletBean、FrameworkServlet、DispatcherServlet 这三个类的创建过程。

通过上面的图，可以看到这三个类直接实现三个接口：EnvironmentCapable、EnvironmentAware、ApplicationContextAware。下面我们直接看下这三个接口的内部是怎样写的。

### EnvironmentCapable

```java
public interface EnvironmentCapable {
    //返回组件的环境，可能返回 null 或者默认环境
    @Nullable
    Environment getEnvironment();
}
```

### EnvironmentAware

```java
public interface EnvironmentAware extends Aware {
    //设置组件的运行环境
    void setEnvironment(Environment environment);
}
```

### ApplicationContextAware

```java
public interface ApplicationContextAware extends Aware {
    //设置运行对象的应用上下文
    //当类实现这个接口后，这个类可以获取ApplicationContext中所有的bean，也就是说这个类可以直接获取Spring配置文件中所有有引用到的bean对象
    void setApplicationContext(ApplicationContext applicationContext) throws BeansException;
}
```



### **HttpServletBean**

这里就直接看其中最重要的 init() 方法的代码了：

```java
@Override
public final void init() throws ServletException {
   //日志代码删除了

   // 从init参数设置bean属性。
   //获得web.xml中的contextConfigLocation配置属性，就是spring MVC的配置文件
   PropertyValues pvs = new ServletConfigPropertyValues(getServletConfig(), this.requiredProperties);
   if (!pvs.isEmpty()) {
      try {
         BeanWrapper bw = PropertyAccessorFactory.forBeanPropertyAccess(this);
         //获取服务器的各种信息
         ResourceLoader resourceLoader = new ServletContextResourceLoader(getServletContext());
         bw.registerCustomEditor(Resource.class, new ResourceEditor(resourceLoader, getEnvironment()));
         //模板方法，可以在子类中调用，做一些初始化工作，bw代表DispatcherServelt
         initBeanWrapper(bw);
         //将配置的初始化值设置到DispatcherServlet中
         bw.setPropertyValues(pvs, true);
      }
      catch (BeansException ex) {
         //日志代码
         throw ex;
      }
   }

   // Let subclasses do whatever initialization they like.
   //模板方法，子类初始化的入口方法
   initServletBean();

   //日志代码删除了
}
```

### **FrameworkServlet**

其中重要方法如下：里面也就两句关键代码，日志代码我直接删掉了

```java
protected final void initServletBean() throws ServletException {
        //日志代码删除了
        long startTime = System.currentTimeMillis();

        //就是 try 语句里面有两句关键代码
        try {
            //初始化 webApplicationContext
            this.webApplicationContext = initWebApplicationContext();
            //模板方法，
            initFrameworkServlet();
        }
        catch (ServletException ex) {
            this.logger.error("Context initialization failed", ex);
            throw ex;
        }
        catch (RuntimeException ex) {
            this.logger.error("Context initialization failed", ex);
            throw ex;
        }

        //日志代码删除了
    }
```

再来看看上面代码中调用的 initWebApplicationContext() 方法

```java
protected WebApplicationContext initWebApplicationContext() {
        //获取 rootContext
        WebApplicationContext rootContext =
                WebApplicationContextUtils.getWebApplicationContext(getServletContext());
        WebApplicationContext wac = null;

        if (this.webApplicationContext != null) {
            // 上下文实例在构造时注入 - >使用它
            wac = this.webApplicationContext;
            if (wac instanceof ConfigurableWebApplicationContext) {
                ConfigurableWebApplicationContext cwac = (ConfigurableWebApplicationContext) wac;
                if (!cwac.isActive()) {
                // 如果上下文尚未刷新 -> 提供诸如设置父上下文，设置应用程序上下文ID等服务
                    if (cwac.getParent() == null) {
            // 上下文实例被注入没有显式的父类 -> 将根应用程序上下文（如果有的话可能为null）设置为父级
                        cwac.setParent(rootContext);
                    }
                    configureAndRefreshWebApplicationContext(cwac);
                }
            }
        }
        if (wac == null) {
    // 当 WebApplicationContext 已经存在 ServletContext 中时，通过配置在 servlet 中的 ContextAttribute 参数获取
            wac = findWebApplicationContext();
        }
        if (wac == null) {
            // 如果 WebApplicationContext 还没有创建，则创建一个
            wac = createWebApplicationContext(rootContext);
        }
        if (!this.refreshEventReceived) {
            // 当 ContextRefreshedEvent 事件没有触发时调用此方法，模板方法，可以在子类重写
            onRefresh(wac);
        }
        if (this.publishContext) {
            // 将 ApplicationContext 保存到 ServletContext 中去
            String attrName = getServletContextAttributeName();
            getServletContext().setAttribute(attrName, wac);
            if (this.logger.isDebugEnabled()) {
                this.logger.debug("Published WebApplicationContext of servlet '" + getServletName() +
                        "' as ServletContext attribute with name [" + attrName + "]");
            }
        }
        return wac;
    }
```

initWebApplicationContext 方法做了三件事：

- 获取 Spring 的根容器 rootContext
- 设置 webApplicationContext 并根据情况调用 onRefresh 方法
- 将 webApplicationContext 设置到 ServletContext 中

这里在讲讲上面代码中的 wac == null 的几种情况：

1）、当 WebApplicationContext 已经存在 ServletContext 中时，通过配置在 servlet 中的 ContextAttribute 参数获取，调用的是 findWebApplicationContext() 方法

```java
protected WebApplicationContext findWebApplicationContext() {
        String attrName = getContextAttribute();
        if (attrName == null) {
            return null;
        }
        WebApplicationContext wac =
                WebApplicationContextUtils.getWebApplicationContext(getServletContext(), attrName);
        if (wac == null) {
            throw new IllegalStateException("No WebApplicationContext found: initializer not registered?");
        }
        return wac;
    }
```

2)、如果 WebApplicationContext 还没有创建，调用的是 createWebApplicationContext 方法

```java
protected WebApplicationContext createWebApplicationContext(@Nullable ApplicationContext parent) {
        //获取创建类型
        Class<?> contextClass = getContextClass();
        //删除了打印日志代码

        //检查创建类型
        if (!ConfigurableWebApplicationContext.class.isAssignableFrom(contextClass)) {
            throw new ApplicationContextException(
                    "Fatal initialization error in servlet with name '" + getServletName() +
                    "': custom WebApplicationContext class [" + contextClass.getName() +
                    "] is not of type ConfigurableWebApplicationContext");
        }
        //具体创建
        ConfigurableWebApplicationContext wac =
                (ConfigurableWebApplicationContext) BeanUtils.instantiateClass(contextClass);

        wac.setEnvironment(getEnvironment());
        wac.setParent(parent);
  //并设置的 contextConfigLocation 参数传给 wac，默认是 WEB-INFO/[ServletName]-Servlet.xml
        wac.setConfigLocation(getContextConfigLocation());

        //调用的是下面的方法
        configureAndRefreshWebApplicationContext(wac);

        return wac;
    }

protected void configureAndRefreshWebApplicationContext(ConfigurableWebApplicationContext wac) {
        if (ObjectUtils.identityToString(wac).equals(wac.getId())) {
            // The application context id is still set to its original default value
            // -> assign a more useful id based on available information
            if (this.contextId != null) {
                wac.setId(this.contextId);
            }
            else {
                // Generate default id...
                wac.setId(ConfigurableWebApplicationContext.APPLICATION_CONTEXT_ID_PREFIX +
                        ObjectUtils.getDisplayString(getServletContext().getContextPath()) + '/' + getServletName());
            }
        }

        wac.setServletContext(getServletContext());
        wac.setServletConfig(getServletConfig());
        wac.setNamespace(getNamespace());
        wac.addApplicationListener(new SourceFilteringListener(wac, new ContextRefreshListener()));

        // The wac environment's #initPropertySources will be called in any case when the context
        // is refreshed; do it eagerly here to ensure servlet property sources are in place for
        // use in any post-processing or initialization that occurs below prior to #refresh
        ConfigurableEnvironment env = wac.getEnvironment();
        if (env instanceof ConfigurableWebEnvironment) {
            ((ConfigurableWebEnvironment) env).initPropertySources(getServletContext(), getServletConfig());
        }

        postProcessWebApplicationContext(wac);
        applyInitializers(wac);
        wac.refresh();
    }
```

里面还有 doXXX() 方法，大家感兴趣的可以去看看。

### **DispatcherServlet**

DispatcherServlet 继承自 FrameworkServlet，onRefresh 方法是 DispatcherServlet 的入口方法，在 initStrategies 方法中调用了 9 个初始化的方法。

![onrefresh](http://ohfk1r827.bkt.clouddn.com/onrefresh.jpg)

这里分析其中一个初始化方法：initLocaleResolver() 方法

```java
private void initLocaleResolver(ApplicationContext context) {
        try {
            //在 context 中获取
            this.localeResolver = context.getBean(LOCALE_RESOLVER_BEAN_NAME, LocaleResolver.class);
            //删除了打印日志的代码
        }
        catch (NoSuchBeanDefinitionException ex) {
            //使用默认的策略
            this.localeResolver = getDefaultStrategy(context, LocaleResolver.class);
            //删除了打印日志的代码
        }
    }
```

查看默认策略代码：

```java
protected <T> T getDefaultStrategy(ApplicationContext context, Class<T> strategyInterface) {
        //调用 getDefaultStrategies 方法
        List<T> strategies = getDefaultStrategies(context, strategyInterface);
        if (strategies.size() != 1) {
            throw new BeanInitializationException(
                    "DispatcherServlet needs exactly 1 strategy for interface [" + strategyInterface.getName() + "]");
        }
        return strategies.get(0);
    }

    
    @SuppressWarnings("unchecked")
    protected <T> List<T> getDefaultStrategies(ApplicationContext context, Class<T> strategyInterface) {
        String key = strategyInterface.getName();
        //根据策略接口的名字从 defaultStrategies 获取所需策略的类型
        String value = defaultStrategies.getProperty(key);
        if (value != null) {
            //如果有多个默认值的话，就以逗号分隔为数组
            String[] classNames = StringUtils.commaDelimitedListToStringArray(value);
            List<T> strategies = new ArrayList<>(classNames.length);
            //按获取到的类型初始化策略
            for (String className : classNames) {
                try {
                    Class<?> clazz = ClassUtils.forName(className, DispatcherServlet.class.getClassLoader());
                    Object strategy = createDefaultStrategy(context, clazz);
                    strategies.add((T) strategy);
                }
                catch (ClassNotFoundException ex) {
                    throw new BeanInitializationException(
                            "Could not find DispatcherServlet's default strategy class [" + className + "] for interface [" + key + "]", ex);
                }
                catch (LinkageError err) {
                    throw new BeanInitializationException(
                            "Error loading DispatcherServlet's default strategy class [" + className + "] for interface [" + key + "]: problem with class file or dependent class", err);
                }
            }
            return strategies;
        }
        else {
            return new LinkedList<>();
        }
    }
```

其他几个方法大概也类似，我就不再写了。

### 小结

主要讲了 Spring MVC 自身创建过程，分析了 Spring MVC 中 Servlet 的三个层次：HttpServletBean、FrameworkServlet 和 DispatcherServlet。HttpServletBean 继承自 Java 的 HttpServlet，其作用是将配置的参数设置到相应的属性上；FrameworkServlet 初始化了 WebApplicationContext；DispatcherServlet 初始化了自身的 9 个组件。

## Spring MVC 流程

分析 Spring MVC 是怎么处理请求的。首先分析 HttpServletBean、FrameworkServlet 和 DispatcherServlet 这三个 Servlet 的处理过程，最后分析 doDispatcher 的结构。

### **HttpServletBean**

参与了创建工作，并没有涉及请求的处理。

### **FrameworkServlet**

在类中的 service() 、doGet()、doPost()、doPut()、doDelete()、doOptions()、doTrace() 这些方法中可以看到都调用了一个共同的方法 processRequest() ，它是类在处理请求中最核心的方法。

```java
protected final void processRequest(HttpServletRequest request, HttpServletResponse response)
            throws ServletException, IOException {

        long startTime = System.currentTimeMillis();
        Throwable failureCause = null;
        //获取 LocaleContextHolder 中原来保存的 LocaleContext
        LocaleContext previousLocaleContext = LocaleContextHolder.getLocaleContext();
        //获取当前请求的 LocaleContext
        LocaleContext localeContext = buildLocaleContext(request);
        //获取 RequestContextHolder 中原来保存的 RequestAttributes
        RequestAttributes previousAttributes = RequestContextHolder.getRequestAttributes();
        //获取当前请求的 ServletRequestAttributes
        ServletRequestAttributes requestAttributes = buildRequestAttributes(request, response, previousAttributes);

        WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);
        asyncManager.registerCallableInterceptor(FrameworkServlet.class.getName(), new RequestBindingInterceptor());
        //将当前请求的 LocaleContext 和 ServletRequestAttributes 设置到 LocaleContextHolder 和 RequestContextHolder
        initContextHolders(request, localeContext, requestAttributes);

        try {
            //实际处理请求的入口，这是一个模板方法，在 Dispatcher 类中才有具体实现
            doService(request, response);
        }catch (ServletException ex) {
            failureCause = ex;
            throw ex;
        }catch (IOException ex) {
            failureCause = ex;
            throw ex;
        }catch (Throwable ex) {
            failureCause = ex;
            throw new NestedServletException("Request processing failed", ex);
        }finally {
            //将 previousLocaleContext，previousAttributes 恢复到 LocaleContextHolder 和 RequestContextHolder 中
            resetContextHolders(request, previousLocaleContext, previousAttributes);
            if (requestAttributes != null) {
                requestAttributes.requestCompleted();
            }
            //删除了日志打印代码
            //发布了一个 ServletRequestHandledEvent 类型的消息
            publishRequestHandledEvent(request, response, startTime, failureCause);
        }
    }
```

**DispatcherServlet**

上一章中其实还没把该类讲清楚，在这个类中，里面的执行处理的入口方法应该是 doService 方法，方法里面调用了 doDispatch 进行具体的处理，在调用 doDispatch 方法之前 doService 做了一些事情：首先判断是不是 include 请求，如果是则对 request 的 Attribute 做个快照备份，等 doDispatcher 处理完之后（如果不是异步调用且未完成）进行还原 ，在做完快照后又对 request 设置了一些属性。

```java
protected void doService(HttpServletRequest request, HttpServletResponse response) throws Exception {
        // Keep a snapshot of the request attributes in case of an include,
        // to be able to restore the original attributes after the include.
        Map<String, Object> attributesSnapshot = null;
        if (WebUtils.isIncludeRequest(request)) {
            attributesSnapshot = new HashMap<>();
            Enumeration<?> attrNames = request.getAttributeNames();
            while (attrNames.hasMoreElements()) {
                String attrName = (String) attrNames.nextElement();
                if (this.cleanupAfterInclude || attrName.startsWith(DEFAULT_STRATEGIES_PREFIX)){
                    attributesSnapshot.put(attrName, request.getAttribute(attrName));
                }
            }
        }
        // Make framework objects available to handlers and view objects.
        request.setAttribute(WEB_APPLICATION_CONTEXT_ATTRIBUTE, getWebApplicationContext());
        request.setAttribute(LOCALE_RESOLVER_ATTRIBUTE, this.localeResolver);
        request.setAttribute(THEME_RESOLVER_ATTRIBUTE, this.themeResolver);
        request.setAttribute(THEME_SOURCE_ATTRIBUTE, getThemeSource());

        FlashMap inputFlashMap = this.flashMapManager.retrieveAndUpdate(request, response);
        if (inputFlashMap != null) {
            request.setAttribute(INPUT_FLASH_MAP_ATTRIBUTE, Collections.unmodifiableMap(inputFlashMap));
        }
        request.setAttribute(OUTPUT_FLASH_MAP_ATTRIBUTE, new FlashMap());
        request.setAttribute(FLASH_MAP_MANAGER_ATTRIBUTE, this.flashMapManager);

        try {
            //调用 doDispatch 方法
            doDispatch(request, response);
        }finally {
            if (!WebAsyncUtils.getAsyncManager(request).isConcurrentHandlingStarted()) {
                // Restore the original attribute snapshot, in case of an include.
                if (attributesSnapshot != null) {
                    restoreAttributesAfterInclude(request, attributesSnapshot);
                }
            }
        }
    }
```

### doDispatch() 方法：

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
        HttpServletRequest processedRequest = request;
        HandlerExecutionChain mappedHandler = null;
        boolean multipartRequestParsed = false;

        WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);
        try {
            ModelAndView mv = null;
            Exception dispatchException = null;
            try {
                //检查是不是上传请求
                processedRequest = checkMultipart(request);
                multipartRequestParsed = (processedRequest != request);

                // Determine handler for the current request.  根据 request 找到 Handler
                mappedHandler = getHandler(processedRequest);
                if (mappedHandler == null || mappedHandler.getHandler() == null) {
                    noHandlerFound(processedRequest, response);
                    return;
                }

    // Determine handler adapter for the current request.根据 Handler 找到对应的 HandlerAdapter
                HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
                // Process last-modified header, if supported by the handler.
                //处理 GET 、 HEAD 请求的 LastModified
                String method = request.getMethod();
                boolean isGet = "GET".equals(method);
                if (isGet || "HEAD".equals(method)) {
                    long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
                    if (logger.isDebugEnabled()) {
                        logger.debug("Last-Modified value for [" + getRequestUri(request) + "] is: " + lastModified);
                    }
                    if (new ServletWebRequest(request, response).checkNotModified(lastModified) && isGet) {
                        return;
                    }
                }
                //执行相应的 Interceptor 的 preHandle
                if (!mappedHandler.applyPreHandle(processedRequest, response)) {
                    return;
                }
                // Actually invoke the handler. HandlerAdapter 使用 Handler 处理请求
                mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
                //如果需要异步处理，直接返回
                if (asyncManager.isConcurrentHandlingStarted()) {
                    return;
                }
                //当 view 为空时，根据 request 设置默认 view
                applyDefaultViewName(processedRequest, mv);
                //执行相应 Interceptor 的 postHandler
                mappedHandler.applyPostHandle(processedRequest, response, mv);
            }catch (Exception ex) {
                dispatchException = ex;
            }catch (Throwable err) {
                // As of 4.3, we're processing Errors thrown from handler methods as well,
                // making them available for @ExceptionHandler methods and other scenarios.
                dispatchException = new NestedServletException("Handler dispatch failed", err);
            }
            //调用 processDispatchResult 方法处理上面处理之后的结果（包括处理异常，渲染页面，发出完成通知触发 Interceptor 的 afterCompletion）
            processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
        }catch (Exception ex) {
            triggerAfterCompletion(processedRequest, response, mappedHandler, ex);
        }catch (Throwable err) {
            triggerAfterCompletion(processedRequest, response, mappedHandler,
                    new NestedServletException("Handler processing failed", err));
        }finally {
          //判断是否执行异步请求
            if (asyncManager.isConcurrentHandlingStarted()) {
                // Instead of postHandle and afterCompletion
                if (mappedHandler != null) {
                    mappedHandler.applyAfterConcurrentHandlingStarted(processedRequest, response);
                }
            }else {
                // Clean up any resources used by a multipart request. 删除上传请求的资源
                if (multipartRequestParsed) {
                    cleanupMultipart(processedRequest);
                }
            }
        }
    }
```

Handler，HandlerMapping，HandlerAdapter 三个区别：

- Handler：处理器，对应 MVC 的 C层，也就是 Controller 层，具体表现形式有很多种，可以是类，方法，它的类型是 Object，只要可以处理实际请求就可以是 Handler。
- HandlerMapping：用来查找 Handler 的。
- HandlerAdapter ：Handler 适配器，

另外 View 和 ViewResolver 的原理与 Handler 和 HandlerMapping 的原理类似。

### 流程图

![doDispatcher](http://ohfk1r827.bkt.clouddn.com/doDispatcher.png)

# SpringJPA



## 常见错误

### Hibernate JAP 字段自动添加下划线问题

数据库中的字段名是productName，实体中应当这样写    

```java
@Column(name = "productname")
private String productName;
```

如果按照数据库中字段productName,一样注解在实体类@Column(name = "productName")

执行sql语句时，会把驼峰字符转化为"_"+ 小写驼峰字符，查询时会变成product_name，所以会报异常:

 error[org.hibernate.exception.SQLGrammarException: Unknown column 'fxenterpri0_.product_name' in 'field list']

sql执行语句：

Hibernate: select fxenterpri0_.id as id9_, fxenterpri0_.product_name asproduct_name  from fx_enterprise fxenterpri0_ order by fxenterpri0_.id desc



# 怎么看Spring的源码

##创建bean需要什么

## 怎么创建Bean

## bean的属性赋值

## bean的销毁

