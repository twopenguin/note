## 数据库性能瓶颈

连接数

数据的体量变大

单台机器的资源限制

## 优化方案

### 读写分离

可以增大连接数

机器性能加强

### 分库分表

#### 垂直拆分

用户一个库，订单一个库，账户一个库

问题：

1.级联查询麻烦，因为都不在同一个库了，甚至不在同一个mysql实例，从而导致系统复杂度变大

2.分布式事务问题

#### 水平拆分

把一张大表，经过一定的拆分规则，拆成小表。

主要是解决单表数据量太大的问题。

