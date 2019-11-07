# 分布式事务

何为分布式事务

一个事务包含多个操作，多个操作操作了多个数据源，这样的事务称为分布式事务。

## 解决方案

强一致性方案

​	XA方案

最终一致性方案

​	最大努力通知

​	TCC补偿

​	可靠消息最终一致性



怎么理解这个强一致性方案，和最终一致性方案，可以这么想，强一致性就是整个分布式事务的每个事务项，必须

同时成功也就是commit，或者同时失败，rollback。

而最终一致性就是，可以部分事务先commit，剩余的事务通过其他的方式保证和已经提交的事务同样的状态。





 XA 是指由 X/Open 组织提出的分布式事务处理的规范. XA 规范主要定义了[事务](https://baike.baidu.com/item/事务)管理器(Transaction Manager)和局部[资源管理器](https://baike.baidu.com/item/资源管理器)(Local Resource Manager)之间的接口 



XA 是跨语言的



JTA 是 Java 基于 XA规范定义的 Java 规范，解释就是Java Transaction Api，



atomiko：上面也说了 JTA 是一种分布式事务 Java 规范。而atomiko 就是其中的一种实现



2pc：是一种一致性协议

3pc、Paxos 也是一种一致性协议



怎么理解一致性协议，一致性协议的作用主要保证多个副本的数据的一致性。







