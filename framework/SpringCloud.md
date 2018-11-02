

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

# SpringCloud概述

## 是什么



## 能干嘛

## 去哪儿下

## 怎么玩

## SpringCloud国内使用情况

