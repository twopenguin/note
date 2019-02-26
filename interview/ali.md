# 阿里面试题

1.项目中学到了什么技术

2.微服务的划分颗粒

3.微服务的高可用怎么保证？

4.常用的负载均衡，该怎么用？你能说下吗？

5.网关能够为后端服务带来哪些好处

7.XML中配置init，destroy方法怎么可以做到调用具体的方法？

8.

10.hashcode和equals方法常用的地方

11.对象比较是否相同

12.hashmap put 方法存放的时候怎么判断是否重复

13.object toString方法常用的地方，为什么要重写该方法

14.set和list的区别

15.

16.

17.set村的顺序是有序的吗？

18.常见set的实现由哪些

19.TreeSet对存入对数据有什么要求

20.hashset的底层实现呢

21.treeset底层源码有看过吗？

22.hashset是不是线程安全的，为什么不是线程安全的

23.java中有哪些线程安全的map

24.

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

37.mysql事物合隔离性和隔离级别

38.不可重复都出现在什么场景

40.前段游览器的一个http请求道后端整个流程是怎么样的？能够说下吗？

42.dns你知道是干嘛的吗？

43.

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

##项目



## 框架

###Spring

项目用Spring比较多，有没有了解过Spring的原理？AOP和IOC的原理

Spring bean的生命周期





## 网络

**TCP和UDP的区别，TCP数据传输过程如何做到可靠的**

------

**http默认端口，https默认端口**

http ： 80

https：443

------



## 性能优化



## Jdk 源码

###集合

**ArralList和LinkedList区别**



------

**hashMap和hashtable区别**

- 1、Hashtable 继承 Dictionary ，HashMap 继承的是 Java2 出现的 Map 接口。
- 2、HashMap 去掉了 Hashtable 的 contains 方法，但是加上了 containsValue 和 containsKey 方法。
- 3、HashMap 允许空键值，而 Hashtable 不允许。
- 4、【重点】4、HashTable 是同步的，而 HashMap 是非同步的，效率上比 HashTable 要高。也因此，HashMap 更适合于单线程环境，而 HashTable 适合于多线程环境。
- 5、HashMap 的迭代器（Iterator）是 fail-fast 迭代器，HashTable的 enumerator 迭代器不是 fail-fast 的。
- 6、HashTable 中数组默认大小是 11 ，扩容方法是 `old * 2 + 1` ，HashMap 默认大小是 16 ，扩容每次为 2 的指数大小。

一般现在不建议用 HashTable 。主要原因是两点：

- 一是，HashTable 是遗留类，内部实现很多没优化和冗余。
- 二是，即使在多线程环境下，现在也有同步的 ConcurrentHashMap 替代，没有必要因为是多线程而用 Hashtable 。

------

**concurrenthashmap是怎么做到线程安全的**



------

**concurrenthashmap 的size实现**

------

**如果存相同数据，ArrayList和LinkedList谁占用空间更大**

LinkedList 明显更大，因为每个节点有很多的额外数据，比如向前和向后的索引

------

**hashmap在并发情况下会出现什么现象**

1、多线程put操作后，get操作导致死循环。
2、多线程put非NULL元素后，get操作得到NULL值。
3、多线程put操作，导致元素丢失。

http://www.cnblogs.com/ITtangtang/p/3966467.html

------

###其他

**Object的hashcode方法重写了，equals方法要不要改？**

------

**JDK中有那几个线程池，顺带把线程池讲下？**

------

object类中的方法？

总共12个方法

wait（三个）、notify、notifyAll、hashcode、equles、toString、clone、getClass、finalize、registerNatives

------

**反射的机制**

------



## 并发

happens before原理



------



------





------



------

## Mysql

**查看SQL是不是使用了索引，有哪些工具**

------

**SQL优化的常见方法有哪些**

------

**SQL索引的顺序，字段的顺序**

------

**sql having的使用场景**

------

**什么是 MVCC ？**

多版本并发控制（MVCC **Multi-Version Concurrency Control**），是一种用来**解决读-写冲突**的无锁并发控制，也就是为事务分配单向增长的时间戳，为每个修改保存一个版本，版本与事务时间戳关联，读操作只读该事务开始前的数据库的快照。 这样在读操作不用阻塞写操作，写操作不用阻塞读操作的同时，避免了脏读和不可重复读。

//TODO

------

## 算法

**说下你知道的排序算法**

------



## 其他

**开发的ide是啥？能说下常用的快捷键吗**



------





