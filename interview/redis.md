#Redis

Redis 其他的数据类型

Geospatial，用来存储一些经纬度，并且能够计算范围，也可以计算两个经纬度的距离



hyperloglog

比如{1，1,2,3,4,5}   有6个数据，但是集合是5个，hyperloglog就主要是来干这个的，用来一些访问量，或者日活UV的统计



特点：

可以使用很小的内存统计非常大的数据量

统计出来的数据可能不准确，

# Zookeeper

## Paxos 算法做什么

Paxos 算法是一个一致性算法，作用是让 Asynchronous non-Byzantine Model 的分布式环境中的各个agent达成一致