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



#### 反射中，Class.forName和ClassLoader区别 



### 设计模式

#### 单例

##### 写出三种单例模式实现 

### 请列出5个运行时异常

### 泛型的存在是用来解决什么问题



##项目

**项目中使用的设计模式**

模板方法、

单例模式、

建造者模式：Protobuf ,生成的协议对象在构建的时候

责任链模式：Netty 的 ChannelPipeline 基于责任链模式开发

## 框架

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

实例化 `BeanFactoryPostProcessor` 实现类 ,

然后调用其 `postProcessBeanFactory` 方法

实例化`BeanPostProcessor` 实现类

实例化  InstantiationAwareBeanPostProcessor 实现类，它也是 `extends BeanPostProcessor`

#### 讲讲Spring加载流程

#### Spring AOP的实现原理

#### 讲讲Spring事务的传播属性

#### Spring如何管理事务的

####Spring怎么解决循环依赖



####XML中配置init，destroy方法怎么可以做到调用具体的方法？



AbstractAutowireCapableBeanFactory 的  initializeBean 方法中，会调用applyBeanPostProcessors 的 before 和 after 方法，然后，这两处调用的中间，有个 `invokeInitMethods` 方法，进入方法，会调用 `InitializingBean` 类的 `afterPropertiesSet` 方法之后，然后会尝试调用 `invokeCustomInitMethod` 



####注解注册bean到ioc容器的几种方式

1. 在上面的配置类上加`@Service`或者`@Component`等注解，springboot会扫描启动类所在的包下面所有带有这些注解的类，实例化bean加到ioc容器。

2. 使用`@Configuration`和`@Bean`注解来配置bean到ioc容器,这个类也需要在springboot启动类所在的包或者子包下面，否则无法扫到。

3. 使用`@Import`注解

   相信很多人对`@EnableScheduling` 、`@EnableCaching`等@Enablexxxx系列的注解都不陌生，它们就是使用的是@Import注解来实现开启xx功能的。


### SpringMVC

#### Springmvc 中DispatcherServlet初始化过程



#### Controller是单例还是多例，怎么保证并发的安全

Controller默认是单例,



### SpringBoot

####SpringBoot starter 原理

首先说说原理，我们知道使用一个公用的starter的时候，只需要将相应的依赖添加的Maven的配置文件当中即可，免去了自己需要引用很多依赖类，并且SpringBoot会自动进行类的自动配置。那么 SpringBoot 是如何知道要实例化哪些类，并进行自动配置的呢？ 下面简单说一下。

首先，SpringBoot 在启动时会去依赖的starter包中寻找 `resources/META-INF/spring.factories `文件，然后根据文件中配置的Jar包去扫描项目所依赖的Jar包，这类似于 Java 的 **SPI** 机制。

第二步，根据 `spring.factories`配置加载`AutoConfigure`类。

最后，根据 `@Conditional`注解的条件，进行自动配置并将Bean注入Spring Context 上下文当中。

我们也可以使用`@ImportAutoConfiguration({MyServiceAutoConfiguration.class})` 指定自动配置哪些类。





####starter 命名格式

我们日常使用的Spring官方的Starter一般采取`spring-boot-starter-{name} `的命名方式，如 `spring-boot-starter-web `。

而非官方的Starter，官方建议 `artifactId` 命名应遵循`{name}-spring-boot-starter `的格式。 例如：`ysc-spring-boot-starter ` 。



### Dubbo

### Mybatis



## 网络

**TCP和UDP的区别，TCP数据传输过程如何做到可靠的**

------

**http默认端口，https默认端口**

http ： 80

https：443

------



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

####HashMap和Hashtable区别

- 1、Hashtable 继承 Dictionary ，HashMap 继承的是 Java2 出现的 Map 接口。
- 2、HashMap 去掉了 Hashtable 的 contains 方法，但是加上了 containsValue 和 containsKey 方法。
- 3、HashMap 允许空键值，而 Hashtable 不允许。
- 4、【重点】4、HashTable 是同步的，而 HashMap 是非同步的，效率上比 HashTable 要高。也因此，HashMap 更适合于单线程环境，而 HashTable 适合于多线程环境。
- 5、HashMap 的迭代器（Iterator）是 fail-fast 迭代器，HashTable的 enumerator 迭代器不是 fail-fast 的。
- 6、HashTable 中数组默认大小是 11 ，扩容方法是 `old * 2 + 1` ，HashMap 默认大小是 16 ，扩容每次为 2 的指数大小。

一般现在不建议用 HashTable 。主要原因是两点：

- 一是，HashTable 是遗留类，内部实现很多没优化和冗余。
- 二是，即使在多线程环境下，现在也有同步的 ConcurrentHashMap 替代，没有必要因为是多线程而用 Hashtable 。

#### 为什么HashMap的容量是2的幂次？

1. 因为位运算的速度比取模快  
2. 如果要使用为位运算取模那么使用这种方式： return h & (length-1);   
3. 使用这种位运算取模的方式，只有当容量为2的幂次的时候，才能使用所有空间



####HashMap在并发情况下会出现什么现象

1、多线程put操作后，get操作导致死循环。
2、多线程put非NULL元素后，get操作得到NULL值。
3、多线程put操作，导致元素丢失。

http://www.cnblogs.com/ITtangtang/p/3966467.html



####ConcurrentHashmap是怎么做到线程安全的

1.7是基于 分段锁，也就是 `Segment`      ,这个类继承与        `ReentrantLock` ，其put方法，火尝试获取锁

1.8中放弃了`Segment`臃肿的设计，取而代之的是采用`Node` + `CAS` + `Synchronized`来保证并发安全进行实现，





####**ConcurrentHashmap的size实现**

在 jdk 1.8 提供了一个新的计数方法`mappingCount `, 它以长整型long返回map中映射的数目。我们应该尽量使用这个新方法，而不是老的size方法， size方法返回的类型为int。 









------

**hashmap put 方法存放的时候怎么判断是否重复**

------

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





## 并发

###偏理论

####happens before原理



####volatile 实现原理

###并发包

#### 原子类的原理是

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

##数据库



###Mysql



#### B+树、B-树的区别

首先B树就是B-树

##### B树

B树是一种多路平衡查找树，它的每个节点最多包含K个孩子，K被称为B树的阶，K的大小取决于磁盘页的大小

下面来具体介绍一下B-树（Balance Tree），一个m阶的B树具有如下几个特征：

1.根结点至少有两个子女。

2.每个中间节点都包含k-1个元素和k个孩子，其中 m/2 <= k <= m

3.每一个叶子节点都包含k-1个元素，其中 m/2 <= k <= m

4.所有的叶子结点都位于同一层。

5.每个节点中的元素从小到大排列，节点当中k-1个元素正好是k个孩子包含的元素的值域分划。

**B树应用**

主要用于文件系统，以及部分数据库索引，比如MongoDB

**一个 m 阶的B树是一个有以下属性的树：**

1. 每一个节点最多有 m 个子节点
2. 每一个非叶子节点（除根节点）最少有 ⌈m/2⌉ 个子节点
3. 如果根节点不是叶子节点，那么它至少有两个子节点
4. 有 k 个子节点的非叶子节点拥有 k − 1 个键

   ​

**B树和B+树的区别**

1. B+树中只有叶子节点会带有指向记录的指针，而B树则所有节点都带有，在内部节点出现的索引项不会再出现在叶子节点中。
2. B+树中所有叶子节点都是通过指针连接在一起，而B树不会。

**B+树优点：**

1. 中间节点全是索引节点，一个是可以降低树的高度，另一个是一个中间节点可以索引到更多的记录
2. 可以直接在叶子节点层横向遍历，b树想要遍历则需要叶子节点和上层节点不停往返。


##### B+树的演进

先是`二分查找树` ,但是二分查找树在极端情况可能是**一条链**，变成了顺序查找===》这个时候`平衡二叉树`出现 ,也叫AVL树，通过左右子树的高度差不能超过1来限制，使得平衡二叉树的查找性能非常好，但是不是最好的（最好的是`最优二叉树`，但是维护建立和维护这种二叉树需要大量的操作，所以一般平衡二叉树即可），其实平衡二叉树也是需要较大的代价来维护平衡，通过一次或者多次的左旋或者右旋操作来维持平衡二叉树的平衡性，

#### 联合索引在B+树中如何存储


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

　　27.与临时表一样，游标并不是不可使用。对小型数据集使用 FAST_FORWARD 游标通常要优于其他逐行处理方法，尤其是在必须引用几个表才能获得所需的数据时。在结果集中包括“合计”的例程通常要比使用游标执行的速度快。如果开发时间允许，基于游标的方法和基于集的方法都可以尝试一下，看哪一种方法的效果更好。 

28. 在所有的存储过程和触发器的开始处设置 SET NOCOUNT ON ，在结束时设置 SET NOCOUNT OFF。无需在执行存储过程和触发器的每个语句后向客户端发送DONE_IN_PROC 消息。 
29. 尽量避免大事务操作，提高系统并发能力。 
30. 尽量避免向客户端返回大数据量，若数据量过大，应该考虑相应需求是否合理。



#### 聚集索引和非聚集索引的区别

聚集索引(聚簇索引或者主键索引):叶子节点存放的是整张表的行记录数据

非聚集索引(二级索引或者非主键索引):叶子节点内容是主键的值,所以使用此类索引后，**回到主键索引树搜索的过程，我们称为回表**





#### Mysql性能优化

1. 增大 join_buffer_size 的数值，也就是表和表联接的缓冲区的大小，在做join操作的时候会把驱动表的相应数据加载进入  joinbuffer 中， 在使用join的时候，基于被驱动表是否有索引有两种情况，分别是 Index Nested-Loop Join  和 Block Nested-Loop Join，后者表示没有索引，如果在后者的情况，如果join-buffer 不够大，一次只能加载部分的驱动表，但是相应的被驱动表就要全表扫描一次，所以增加此buffer 能降低被驱动表的全表扫描次数从而提升性能

**SQL索引的顺序，字段的顺序**

基于索引的最左匹配特性？



**sql having的使用场景**

####什么是 MVCC ？

多版本并发控制（MVCC **Multi-Version Concurrency Control**），是一种用来**解决读-写冲突**的无锁并发控制，也就是为事务分配单向增长的时间戳，为每个修改保存一个版本，版本与事务时间戳关联，读操作只读该事务开始前的数据库的快照。 这样在读操作不用阻塞写操作，写操作不用阻塞读操作的同时，避免了脏读和不可重复读。

//TODO

------

**不可重复都出现在什么场景**

------

**mysql事物合隔离性和隔离级别**

------

## 算法与数据结构

**说下你知道的排序算法**





## JVM

###JVM 垃圾回收算法？

1. 标记-清除算法
2. 标记-整理算法
3. 复制算法
4. 分代收集算法

###JVM垃圾回收机制，何时触发MinorGC等操作

对象优先在新生代 Eden 区中分配，如果 Eden 区没有足够的空间时，就会触发一次 Young GC 。



###什么情况下回出现 Full GC？

- 1、在执行 Young GC 之前，JVM 会进行空间分配担保——如果老年代的连续空间小于新生代对象的总大小（或历次晋升的平均大小），则触发一次 Full GC 。
- 2、大对象直接进入老年代，从年轻代晋升上来的老对象，尝试在老年代分配内存时，但是老年代内存空间不够。
- 3、显式调用 `System#gc()` 方法时。



####直接内存（堆外内存）与堆内存比较？

1. 直接内存申请空间耗费更高的性能，当频繁申请到一定量时尤为明显。
2. 直接内存 IO 读写的性能要优于普通的堆内存，在多次读写操作的情况下差异明显。

------

1. ​

------

###内存泄漏的可能原因

1. 静态集合类引起内存泄漏，如HashMap，ArrayList等
2. 各种连接，比如数据库连接（dataSourse.getConnection()），网络连接(socket)和io连接，除非其显式的调用了其close（）方法将其连接关闭，否则是不会自动被GC 回收的。
3. 单例模式，不正确使用单例模式是引起内存泄漏的一个常见问题，单例对象在初始化后将在JVM的整个生命周期中存在（以静态变量的方式），如果单例对象持有外部的引用。



#### 当出现了内存溢出，你怎么排错



##Linux

## 分布式

### 理论



#### 一致性hash算法原理与应用

####  CAP原则

#### 分布式raft算法

### 做服务发现常用的框架

- zookeeper
- eureka
- etcd
- consul












