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

看看这个类的属性，大概也能猜到我们能使用的`server` 开头的参数，都是在这个类里面定义的，比如`port` `contextPath`等等

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



