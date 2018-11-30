# 简介

本地缓存

Ehcache是Hibernate 中默认的CacheProvider，hibernate就是使用Ehcache来实现二级缓存的

Ehcache还支持分布式的缓存

## 常见缓存管理器

\* Generic 
\* JCache (JSR-107) 
\* EhCache 2.x 
\* Hazelcast 
\* Infinispan 
\* Redis 
\* Guava 

\* Simple



# Spring 的缓存整合

spring boot本身是提供数据缓存的功能，SpringBoot自带的cache技术我想大家都应该用过，为了解决数据库输入输出的瓶颈所以一般情况下我们都会引入非常多的缓存策略，例如引入redis缓存，引入Hibernate的二级缓存等等。

SpringBoot在annotation的层面给我们实现了cache，当然这也是得益于Spring的AOP。所有的缓存配置只是在annotation层面配置，完全没有侵入到我们的代码当中，就像我们的声明式事务一样。

Spring定义了CacheManager和Cache接口统一不同的缓存技术。其中CacheManager是Spring提供的各种缓存技术的抽象接口。而Cache接口包含缓存的各种操作，当然我们一般情况下不会直接操作Cache接口。

Spring针对不同的缓存技术，需要实现不同的cacheManager，Spring定义了如下的cacheManger实现

cacheManager实现
CacheManger	描述
SimpleCacheManager	使用简单的Collection来存储缓存，主要用于测试
ConcurrentMapCacheManager	使用ConcurrentMap作为缓存技术（默认）
NoOpCacheManager	测试用
EhCacheCacheManager	使用Ehcache作为缓存技术，以前在HIbernate的时候经常用
GuavaCacheManager	使用google guava的GuavaCache作为缓存技术
HazelcastCacheManager	使用Hazelcast作为缓存技术
JCacheCacheManager	使用JCache标准的实现作为缓存技术，如Apache Commons JCS

RedisCacheManager	使用Redis作为缓存技术

