# 问题待解决

dubbo中"读接口"和"写接口"有什么区别?

谈谈dubbo中的负载均衡算法及特点？

简单谈谈你对一致性哈希算法的认识？

服务发布过程中做了哪些事？

dubbo都有哪些协议,他们之间有什么特点,缺省值是什么？

什么是本地暴露和远程暴露,他们的区别？

服务提供者能实现失效踢出是根据什么原理?

讲讲dubbo服务暴露中本地暴露,并画图辅助说明？

一般选择什么注册中心,还有别的选择吗?

dubbo中zookeeper做注册中心,如果注册中心集群都挂掉,那发布者和订阅者还能通信吗?(面试高频题)

项目中有使用过多线程吗?有的话讲讲你在哪里用到了多线程?(面试高频题)

(高频题)

zookeeper的有哪些节点,他们有什么区别?讲一下应用场景。

画一画服务注册与发现的流程图。

在dubbo中,什么时候更新本地的zookeeper信息缓存文件?订阅zookeeper信息的整体过程是怎么样的?

谈一下你们项目架构设计(很多人在回答这个的时候都容易回答SSH或者SSM,注意,所谓是SSH这些是技术选型,不是架构的设计)

既然你们项目用到了dubbo,那你讲讲你们是怎么通过dubbo实现服务降级的,降级的方式有哪些,又有什么区别?

dubbo监控平台能够动态改变接口的一些设置,其原理是怎样的?

既然你说你看过dubbo源码,那讲一下有没有遇到过什么坑?(区分度高,也是检验是否看过源码的试金石)

dubbo的原理是怎么样的?请简单谈谈

有没有考虑过自己实现一个类似dubbo的RPC框架,如果有,请问你会如果着手实现?(面试高频题,区分度高)

你说你用过mybatis,那你知道Mapper接口的原理吗?(如果回答得不错,并且提到动态代理这个关键词会继续往下问,那这个动态代理又是如何通过依赖注入到Mapper接口的呢?)

描述一下dubbo服务引用的过程,原理

既然你提到了dubbo的服务引用中封装通信细节是用到了动态代理,那请问创建动态代理常用的方式有哪些,他们又有什么区别?dubbo中用的是哪一种?(高频题)

除了JDK动态代理和CGLIB动态代理外,还知不知道其他实现代理的方式?(区分度高)

你是否了解spi,讲一讲什么是spi,为什么要使用spi?

对类加载机制了解吗,说一下什么是双亲委托模式,他有什么弊端,这个弊端有没有什么我们熟悉的案例,解决这个弊端的原理又是怎么样的?

既然你对spi有一定了解,那么dubbo的spi和jdk的spi有区别吗?有的话,究竟有什么区别?

你提到了dubbo中spi也增加了IoC,那你先讲讲Spring的IoC,然后再讲讲dubbo里面又是怎么做的？

你提到了dubbo中spi也增加了AOP,那你讲讲这用到了什么设计模式,dubbo又是如何做的？



# 服务注册

从``ServiceConfig``  的 `export`开始服务注册

依次进入`doExportUrls`方法：

```java
@SuppressWarnings({"unchecked", "rawtypes"})
private void doExportUrls() {
    List<URL> registryURLs = loadRegistries(true);
    for (ProtocolConfig protocolConfig : protocols) {
        String pathKey = URL.buildKey(getContextPath(protocolConfig).map(p -> p + "/" + path).orElse(path), group, version);
        ProviderModel providerModel = new ProviderModel(pathKey, ref, interfaceClass);
        ApplicationModel.initProviderModel(pathKey, providerModel);
        doExportUrlsFor1Protocol(protocolConfig, registryURLs);
    }
}
```



1.加载所有的注册中心的地址：`loadRegistries(true)`





# 服务发现

