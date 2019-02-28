#配置项（比如server.port）

##简介

在我们使用SpringBoot的 时候，可以配置一些参数，比如`server.port`那么原理呢？

看看这个类：

```java
@ConfigurationProperties(prefix = "server", ignoreUnknownFields = true)
public class ServerProperties
		implements EmbeddedServletContainerCustomizer, EnvironmentAware, Ordered {

	/**
	 * Server HTTP port.
	 */
	private Integer port;

	/**
	 * Network address to which the server should bind to.
	 */
	private InetAddress address;

	/**
	 * Context path of the application.
	 */
	private String contextPath;
}
```

看看这个类的属性，大概也能猜到我们能使用的`server` 开头的参数，都是在这个类里面定义的，比如`port` `contextPath`等等。



## 查看可以使用哪些配置项

使用idea的全局搜索功能，搜索SpringBoot包中下面的关键字，就可以查出大概可以使用哪些关键字了

`@ConfigurationProperties(`



# 其他小技巧

## Banner操作

### 修改Banner

在 src/main/resources 下新建 banner.txt 文件

到网站 <http://patorjk.com/software/taag-v1/> 上生成自己想要的横幅，拷贝到 上面的banner.txt中

### 关闭 Banner

```java
SpringApplication application = new SpringApplication(args);
application.setBannerMode(Banner.Mode.OFF);
```



# 注解

## @ConfigurationProperties

`@ConfigurationProperties` 就可以将配置文件自动设置到该 Bean 对象

### 如何使用

我们可以在`application.properties` 文件中配置

```pr
server.port=8080
server.contextPath=/hello
```

是因为有如下的一个配置类

```java
@ConfigurationProperties(
    prefix = "server",
    ignoreUnknownFields = true
)
public class ServerProperties implements EmbeddedServletContainerCustomizer, EnvironmentAware, Ordered {
    private Integer port;
    private InetAddress address;
    private String contextPath;
}
```



## @EnableAutoConfiguration

可以将指定带有 `@ConfigurationProperties` 的类，注册成 BeanDefinition ，从而创建成 Bean 对象。

### 是什么

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@AutoConfigurationPackage
@Import({EnableAutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {
    String ENABLED_OVERRIDE_PROPERTY = "spring.boot.enableautoconfiguration";
    Class<?>[] exclude() default {};
    String[] excludeName() default {};
}
```

从`@Import` 注解上可以看出 使用 `EnableConfigurationPropertiesImportSelector` 来处理

### EnableAutoConfigurationImportSelector





# 疑问

1.为什么主启动类所在的目录下的所以目录都是`ComponentScan` 所能扫描的范围？



