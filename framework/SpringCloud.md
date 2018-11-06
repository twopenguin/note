

# SpringCloud面试题

什么是微服务？

微服务之间是如何独立通讯的？

SpringCloud 和 Dubbo有哪些区别？
SpringBoot 和 SpringCloud，请你谈谈对他们的理解？
什么是服务熔断？什么是服务降级？
微服务的优缺点分别是什么？说下你在项目开发中碰到的坑

你锁知道的微服务的技术栈有哪些？请列举一二

Eureka 和 Zookeeper 都可以提供服务注册与发现的功能，请说说两个的区别？

# 微服务概述

##微服务和微服务架构

### 微服务

建议先看看下面的描述什么是`微服务架构`，了解了微服务架构，那么微服务就是一个单独的小的服务，业务单一，独立部署，强调的是服务的大小，它关注的是某一个点，是具体解决某一个问题/提供落地对应服务的一个服务应用。

### 微服务架构

什么是微服务？

​	业界大牛马丁.福勒（Martin Fowler），[这样描述微服务](](https://martinfowler.com/articles/microservices.html))

```
The term "Microservice Architecture" has sprung up over the last few years to describe a particular way of designing software applications as suites of independently deployable services. While there is no precise definition of this architectural style, there are certain common characteristics around organization around business capability, automated deployment, intelligence in the endpoints, and decentralized control of languages and data.
```



就目前而言，对于微服务业界并没有一个统一的标准的定义。

但通常而言，微服务架构是一种架构模式或者说架构风格，它提倡将**单一应用程序划分成一组小的服务**，每个服务运行在其**独立的自己的进程**中，服务之间互相协调、互相配合，为用户提供最终价值，服务之间采用轻量级的通信机制互相沟通(通常是基于Http的RESTful API)。每个服务都围绕着具体业务进行构建，并且能够独立的部署到生产环境、类生产环境等。另外，应尽量避免统一、集中式的服务管理机制，对具体的一个服务而言，应根据业务上下文，选择合适的语言、工具对其进行构建，可以有一个非常轻量级的集中式管理来协调这些服务，可以使用不同的语言来编写服务，也可以使用不同的数据存储。

### 总结

微服务就是一个功能单一的小服务，而众多的微服务组成一个大型的系统，对应能给用户提供完整的功能，这样的架构叫微服务架构。

## 微服务的优缺点

### 优点

每个服务足够内聚、足够小、代码容易理解这样能聚焦一个指定的业务功能或业务需求。

开发简单、开发效率提高，一个服务可能就是专一的只干一件事。

微服务能够被小团队单独开发，这个小团队是2到5人的开发人员组成。

微服务是松耦合的，是有功能意义的服务，无论是在开发阶段还是部署阶段都是独立的。

微服务能使用不同的语言开发。

易于和第三方集成，微服务允许容易且灵活的方式集成自动部署，通过持续集成工具，如Jenkins，Hudson，Bambo。

微服务易于被一个开发人员理解，修改和维护，这样小团队能够更关注自己的工作成果。无需通过合作才能实现价值。

微服务允许你融合最新技术。

微服务只是业务逻辑的代码，不会和HTML、CSS或其他的界面组件组合。

每个微服务都有自己的存储能力，可以有自己的数据库，也可以有统一数据库。

### 缺点

开发人员要处理分布式系统的复杂性

多服务运维难度，随着服务的增加，运维的压力也在增大。

系统部署依赖

服务间通信成本

数据一致性

系统集成测试

性能监控。。。

## 微服务技术栈有哪些

什么是微服务技术栈呢？就是一个算是微服务的架构那么就需要在项目中有一些服务调用、服务注册与发现、负载均衡等等技术。这些技术就像我们平时接触的java的接口一样，有对应的落地实现，就像实现类。下面我们来看看：

| 微服务条目                | 落地技术                                     | 备注   |
| -------------------- | ---------------------------------------- | ---- |
| 服务开发                 | SpringBoot、Spring、SpringMVC              |      |
| 服务配置与管理              | Netflix公司的Archaius、阿里的Diamond等           |      |
| 服务注册与发现              | Eureka、Consul、Zookeeper等                 |      |
| 服务调用                 | Rest、RPC、gRPC                            |      |
| 服务熔断器                | Hystrix、Envoy等                           |      |
| 负载均衡                 | Ribbon、Nginx等                            |      |
| 服务接口调用（客户端调用服务的简化工具） | Feign等                                   |      |
| 消息队列                 | Kafka、RabbitMQ、ActiveMQ等                 |      |
| 服务器配置中心管理            | SpringCloudConfig、Chef等                  |      |
| 服务路由(API网关)          | Zuul等                                    |      |
| 服务监控                 | Zabbix、Nggios、Metrics、Spectator等         |      |
| 全链路追踪                | Zipkin、Brave、Dapper等                     |      |
| 服务部署                 | Docker、OpenStack、Kubernetes等             |      |
| 数据流操作开发包             | SpringCloud Stream(封装与Redis、Rabbit、Kafka等发送接收消息) |      |
| 事件消息总线               | SpringCloud Bus                          |      |

## 微服务架构选型依据

整体解决方案和框架成熟度

社区热度

可维护性

学习曲线

## 目前各大IT公司的微服务架构有哪些？

阿里Dubbo/HSF(好舒服)

京东JSF(京舒服)

新浪微博Motan

当当网Dubbox

## 各微服务框架对比

# SpringCloud

## SpringCloud概述

### 是什么

先看看官网说明，[看这里](spring.io)

#### COORDINATE ANYTHING: DISTRIBUTED SYSTEMS SIMPLIFIED

Building distributed systems doesn't need to be complex and error-prone. Spring Cloud offers a simple and accessible programming model to the most common distributed system patterns, helping developers build resilient, reliable, and coordinated applications. Spring Cloud is built on top of Spring Boot, making it easy for developers to get started and become productive quickly.

SpringCloud=分布式微服务架构下的一站式解决方案，是各个微服务架构落地技术的集合体。

### 能干嘛

### 怎么学习

官网：

http://projects.spring.io/spring-cloud/

参考书：

Spring Cloud Netflix 中文参考文档：https://springcloud.cc/spring-cloud-netflix.html

API说明：

```java
http://cloud.spring.io/spring-cloud-static/Dalston.SR1/
https://springcloud.cc/spring-cloud-dalston.html
```

SpringCloud中国社区：http://springcloud.cn/

SpringCloud中文网：http://springcloud.cc/



### 怎么玩

### SpringCloud国内使用情况

各个大厂都在使用SpringCloud，联通，华为等等

阿里云也在使用

## SpringBoot和SpringCloud的关系

SpringBoot 专注于快速开发单个体微服务。

SpringCloud是关注全局的微服务协调治理框架，它将SpringBoot开发的一个个单体微服务整合病管理起来。为哥哥微服务之间提供，配置管理、服务发现、断路器、路由、微代理、事件总线、全局锁、决策竞选、分布式会话等等集成服务。

SpringBoot可以离开SpringCloud独立使用开发项目。但是SpringCloud离不开SpringBoot。属于依赖关系。

**SpringBoot专注于快速、方便的开发单个微服务个体，SpringCloud关注全局的服务治理框架。**

## SpringCloud 和 Dubbo的区别

先来看一张表对比一下：

| 微服务技术  | Dubbo         | SpringCloud                  |
| ------ | ------------- | ---------------------------- |
| 服务注册中心 | Zookeeper     | Spring Cloud Netflix Eureka  |
| 服务调用方式 | RPC           | REST API                     |
| 服务监控   | Dubbo-monitor | SpringBoot Admin             |
| 断路器    | 不完善           | Spring Cloud Netflix Hystnix |
| 服务网关   | 无             | Spring Cloud Netflix Zuul    |
| 分布式配置  | 无             | Spring Cloud Config          |
| 服务跟踪   | 无             | Spring Cloud Sleuth          |
| 消息总线   | 无             | Spring Cloud Bus             |
| 数据流    | 无             | Spring Cloud Stream          |
| 批量任务   | 无             | Spring Cloud Task            |

### 社区活跃度



**最大的区别：SpringCloud抛弃了Dubbo的RPC通信，采用的是基于HTTP的REST方式**



## RestTemplet

它提供了多做便捷访问远程Http服务的的方法。

是一种简单便捷的访问restful 服务模板类，是spring 提供的用于访问Rest服务的**客户端模板工具**集



[官网点我](https://docs.spring.io/spring/docs/3.0.x/javadoc-api/org/springframework/web/client/RestTemplate.html)



使用总规则：
使用RestTemplate 访问restful服务接口非常的方便无脑

(url, requestMap, ResponseBean.class) 这三个参数分别代表

REST 请求地址，请求参数，HTTP响应转换成的对象类型

### 常见用法

```java
    public static final String REST_URL_PREFIX = "http://localhost:8001";

    @Autowired
    private RestTemplate restTemplate;

    /**
     * add 操作
     * @param dept
     */
    @RequestMapping(value = "/consumer/dept/add")
    public boolean add(Dept dept){
        return restTemplate.postForObject(REST_URL_PREFIX+"/dept/add", dept, Boolean.class);
    }

    /**
     * get 操作
     * @param id
     */
    @RequestMapping(value = "/consumer/dept/get/{id}")
    public Dept get(@PathVariable Long id){
        return restTemplate.getForObject(REST_URL_PREFIX+"/dept/get/"+id,Dept.class);
    }

    /**
     * list 操作
     */
    @RequestMapping(value = "/consumer/dept/list")
    public List<Dept> list(){
        return restTemplate.getForObject(REST_URL_PREFIX+"/dept/list" , List.class);
    }
```



# Eureka

## 是什么

参看[官网](https://github.com/Netflix/eureka)

简单说说就是：

Eureka 是 Netflix 的一个子模块，也是核心模块之一。Eureka 是一个基于REST 的服务，用于定位服务，以实现云端中间层服务发现和故障转移。服务注册与发现对于微服务架构来说非常重要的，有了服务注册与发现，**只需要使用服务的标识符，就可以访问到服务，而不需要修改服务调用的配置文件了。功能类似`dubbo`的注册中心，比如Zookeeper。**



**Netflix 在设计 Eureka时遵守的是AP原则。**



## 原理

Spring Cloud 封装了 Netflix 公司开发的 Eureka,来实现服务注册和发现

Eureka 采用了 C - S 的设计架构，Eureka Server 作为服务注册功能的的服务器， 它是服务注册中心。

而系统中的其他微服务，使用Eureka 的客户端连接到 Eureka Server 并维持心跳连接，这样系统的维护人员就可以通过 Eureka Server 来监控各个微服务是否正常运行，SpringCloud 的一些其他模块（比如Zuul）就可以通过Eureka Server 来发现系统中的其他服务，并执行相关逻辑。



Eureka 包含两大组件：Eureka Server 和 Eureka Client

Eureka Server 提供服务注册服务

各个节点启动后，会在 Eureka Server 中进行注册，这样Eureka Server中的服务注册表中将会存储所有可用服务节点信息，服务节点的信息可以在界面中直观的看到。

Eureka Client 是一个Java 客户端，用于简化 与Eureka Server的交互，客户端同时也具备一个内置的、使用轮询(round-robin)负载算法的负载均衡器。在应用启动后，将回向 Eureka Server 发送心跳（默认周期是30秒），如果Eureka Server 在多个心跳周期内没有接收到某个节点的心跳，Eureka Srever 将会从服务器注册表中把这个服务节点移除（默认是90秒）。









