---
typora-copy-images-to: picture
---

# 安装

建议放在/opt 目录下面

# 存储引擎

查看当前的 mysql 提供了哪些存储引擎：

```mysql
show engines;
```

查看当前使用的引擎:

```mysql
show variables like '%storage_engine%';
```

MyISAM存储引擎  VS  InnoDB存储引擎：

![1522682224326](picture/1522682224326.png)

# Sql 性能

## Sql执行顺序

我们写Sql 语句有一个顺序，但是mysql 在解析执行我们的sql语句的时候有它自己的解读顺序

(7)     SELECT   

(8)     DISTINCT <select_list>  

(1)     FROM <left_table>  

(3)     <join_type> JOIN <right_table>  

(2)     ON <join_condition>  

(4)     WHERE <where_condition>  

(5)     GROUP BY <group_by_list>  

(6)     HAVING <having_condition>  

(9)     ORDER BY <order_by_condition>  

(10)    LIMIT <limit_number>  

我们正常写的时候就只从select 开始的，然后一直往下，但是机器的顺序是根据**（）括号**里面的顺序来的

下面来一步一步解释一下：

一、执行`FROM`语句，mysql是从左往右执行，oracle是从右往左执行，

SQL语句的执行过程中，都会产生一个虚拟表，用来保存SQL语句的执行结果（这是重点），执行from语句之后会产生一个**虚拟表**暂时叫VT1（vitual table 1），VT1是根据笛卡尔积生成



二、执行on进行过滤

根据on后面的条件过滤掉不符合条件的数据，参数VT2

三、执行链接的类型

inner join内连接、left join左链接、right右链接、outer join 外链接、fullouter join 全连接

执行完产生VT3

四、执行where后面的条件 

这时候使用WHERE条件的时候要注意：不能使用组函数、并且字段的别名不能放到条件中使用

例如SELECT city as c FROM t WHERE c='shanghai'

五、执行group by 进行分组

六、执行having过滤

`HAVING`子句主要和`GROUP
 BY`子句配合使用，having后面可以跟组函数的条件

七、执行select

八、执行distinct，去掉重复的数据

九、执行order by 语句排序

十、执行分页语句

## 为什么Sql查询慢

应该从两个方面来考虑：

1.sql执行时间长,

​	sql写的很烂，

​		没有使用索引

​		关联查询太多

​	索引失效(就是建了索引但是没有用上)

2.等待时间长：

​	mysql服务器的的参数设置不够好，（比如线程数，缓存等等），然后一直在等待获取资源好执行

# 关键字

## Join

###七种Join

#### Inner JOIN

![INNER_JOIN.png](https://www.codeproject.com/KB/database/Visual_SQL_Joins/INNER_JOIN.png)

This is the simplest, most understood Join and is the most common. This query will return all of the records in the left table (table A) that have a matching record in the right table (table B). This Join is written as follows:

```mysql
SELECT <select_list> 
FROM Table_A A
INNER JOIN Table_B B
ON A.Key = B.Key
```

#### Left JOIN

![LEFT_JOIN.png](https://www.codeproject.com/KB/database/Visual_SQL_Joins/LEFT_JOIN.png)

This query will return all of the records in the left table (table A) regardless if any of those records have a match in the right table (table B). It will also return any matching records from the right table. This Join is written as follows:

```mysql
SELECT <select_list>
FROM Table_A A
LEFT JOIN Table_B B
ON A.Key = B.Key
```

#### Right JOIN

![RIGHT_JOIN.png](https://www.codeproject.com/KB/database/Visual_SQL_Joins/RIGHT_JOIN.png)

This query will return all of the records in the right table (table B) regardless if any of those records have a match in the left table (table A). It will also return any matching records from the left table. This Join is written as follows:

```mysql
SELECT <select_list>
FROM Table_A A
RIGHT JOIN Table_B B
ON A.Key = B.Key
```

#### Outer JOIN

![FULL_OUTER_JOIN.png](https://www.codeproject.com/KB/database/Visual_SQL_Joins/FULL_OUTER_JOIN.png)

This Join can also be referred to as a `FULL OUTER JOIN` or a `FULL JOIN`. This query will return all of the records from both tables, joining records from the left table (table A) that match records from the right table (table B). This Join is written as follows:

**（PS：Mysql 不支持 FULL JOIN 或者 FULL OUTER JOIN 或者 OUTER JOIN ，这三个都是一个意思，Oracle支持）**

```mysql
SELECT <select_list>
FROM Table_A A
FULL OUTER JOIN Table_B B
ON A.Key = B.Key
```

#### Left Excluding JOIN

![LEFT_EXCLUDING_JOIN.png](https://www.codeproject.com/KB/database/Visual_SQL_Joins/LEFT_EXCLUDING_JOIN.png)

This query will return all of the records in the left table (table A) that do not match any records in the right table (table B). This Join is written as follows:

```mysql
SELECT <select_list> 
FROM Table_A A
LEFT JOIN Table_B B
ON A.Key = B.Key
WHERE B.Key IS NULL
```

####Right Excluding JOIN

![RIGHT_EXCLUDING_JOIN.png](https://www.codeproject.com/KB/database/Visual_SQL_Joins/RIGHT_EXCLUDING_JOIN.png)

This query will return all of the records in the right table (table B) that do not match any records in the left table (table A). This Join is written as follows:

```mysql
SELECT <select_list>
FROM Table_A A
RIGHT JOIN Table_B B
ON A.Key = B.Key
WHERE A.Key IS NULL
```

####Outer Excluding JOIN

![OUTER_EXCLUDING_JOIN.png](https://www.codeproject.com/KB/database/Visual_SQL_Joins/OUTER_EXCLUDING_JOIN.png)

This query will return all of the records in the left table (table A) and all of the records in the right table (table B) that do not match. I have yet to have a need for using this type of Join, but all of the others, I use quite frequently. This Join is written as follows:

```mysql
SELECT <select_list>
FROM Table_A A
FULL OUTER JOIN Table_B B
ON A.Key = B.Key
WHERE A.Key IS NULL OR B.Key IS NULL
```

### Join 总结

![img](https://www.codeproject.com/KB/database/Visual_SQL_Joins/Visual_SQL_JOINS_V2.png)

### 疑点自测

###获取笛卡尔积

两张表一张表八条记录，一张表两条记录，怎么获得两周表的笛卡尔积；

表A如下：

![1522756914831](picture/1522756914831.png)

表B如下：

![1522756946971](picture/1522756946971.png)

使用如下的语句获得笛卡尔积：（Join 可加可不加）

```mysql
select * from table_a join table_b;
<!-- 或者 -->
select * from table_a, table_b;
```

结果如下：

![1522757078815](picture/1522757078815.png)







### 七种Join测试

```
TABLE_A
  PK Value
---- ----------
   1 FOX
   2 COP
   3 TAXI
   6 WASHINGTON
   7 DELL
   5 ARIZONA
   4 LINCOLN
  10 LUCENT

TABLE_B
  PK Value
---- ----------
   1 TROT
   2 CAR
   3 CAB
   6 MONUMENT
   7 PC
   8 MICROSOFT
   9 APPLE
  11 SCOTCH
```

The results of the seven Joins are shown below:

```
-- INNER JOIN
SELECT A.PK AS A_PK, A.Value AS A_Value,
       B.Value AS B_Value, B.PK AS B_PK
FROM Table_A A
INNER JOIN Table_B B
ON A.PK = B.PK

A_PK A_Value    B_Value    B_PK
---- ---------- ---------- ----
   1 FOX        TROT          1
   2 COP        CAR           2
   3 TAXI       CAB           3
   6 WASHINGTON MONUMENT      6
   7 DELL       PC            7

(5 row(s) affected)
```

Hide    Copy Code

```
-- LEFT JOIN
SELECT A.PK AS A_PK, A.Value AS A_Value,
B.Value AS B_Value, B.PK AS B_PK
FROM Table_A A
LEFT JOIN Table_B B
ON A.PK = B.PK

A_PK A_Value    B_Value    B_PK
---- ---------- ---------- ----
   1 FOX        TROT          1
   2 COP        CAR           2
   3 TAXI       CAB           3
   4 LINCOLN    NULL       NULL
   5 ARIZONA    NULL       NULL
   6 WASHINGTON MONUMENT      6
   7 DELL       PC            7
  10 LUCENT     NULL       NULL

(8 row(s) affected)
```



```
-- RIGHT JOIN
SELECT A.PK AS A_PK, A.Value AS A_Value,
B.Value AS B_Value, B.PK AS B_PK
FROM Table_A A
RIGHT JOIN Table_B B
ON A.PK = B.PK

A_PK A_Value    B_Value    B_PK
---- ---------- ---------- ----
   1 FOX        TROT          1
   2 COP        CAR           2
   3 TAXI       CAB           3
   6 WASHINGTON MONUMENT      6
   7 DELL       PC            7
NULL NULL       MICROSOFT     8
NULL NULL       APPLE         9
NULL NULL       SCOTCH       11

(8 row(s) affected)
```



```
-- OUTER JOIN
SELECT A.PK AS A_PK, A.Value AS A_Value,
B.Value AS B_Value, B.PK AS B_PK
FROM Table_A A
FULL OUTER JOIN Table_B B
ON A.PK = B.PK

A_PK A_Value    B_Value    B_PK
---- ---------- ---------- ----
   1 FOX        TROT          1
   2 COP        CAR           2
   3 TAXI       CAB           3
   6 WASHINGTON MONUMENT      6
   7 DELL       PC            7
NULL NULL       MICROSOFT     8
NULL NULL       APPLE         9
NULL NULL       SCOTCH       11
   5 ARIZONA    NULL       NULL
   4 LINCOLN    NULL       NULL
  10 LUCENT     NULL       NULL

(11 row(s) affected)
```



```
-- LEFT EXCLUDING JOIN
SELECT A.PK AS A_PK, A.Value AS A_Value,
B.Value AS B_Value, B.PK AS B_PK
FROM Table_A A
LEFT JOIN Table_B B
ON A.PK = B.PK
WHERE B.PK IS NULL

A_PK A_Value    B_Value    B_PK
---- ---------- ---------- ----
   4 LINCOLN    NULL       NULL
   5 ARIZONA    NULL       NULL
  10 LUCENT     NULL       NULL
(3 row(s) affected)
```

Hide    Copy Code

```
-- RIGHT EXCLUDING JOIN
SELECT A.PK AS A_PK, A.Value AS A_Value,
B.Value AS B_Value, B.PK AS B_PK
FROM Table_A A
RIGHT JOIN Table_B B
ON A.PK = B.PK
WHERE A.PK IS NULL

A_PK A_Value    B_Value    B_PK
---- ---------- ---------- ----
NULL NULL       MICROSOFT     8
NULL NULL       APPLE         9
NULL NULL       SCOTCH       11

(3 row(s) affected)
```

Hide    Copy Code

```
-- OUTER EXCLUDING JOIN
SELECT A.PK AS A_PK, A.Value AS A_Value,
B.Value AS B_Value, B.PK AS B_PK
FROM Table_A A
FULL OUTER JOIN Table_B B
ON A.PK = B.PK
WHERE A.PK IS NULL
OR B.PK IS NULL

A_PK A_Value    B_Value    B_PK
---- ---------- ---------- ----
NULL NULL       MICROSOFT     8
NULL NULL       APPLE         9
NULL NULL       SCOTCH       11
   5 ARIZONA    NULL       NULL
   4 LINCOLN    NULL       NULL
  10 LUCENT     NULL       NULL

(6 row(s) affected)
```

## exists & in

这节主要是来区分这两个关键字的区别：

case 1：

![1530684975440](picture/1530684975440.png)

case 2：

![1530685026634](picture/1530685026634.png)



case 3：

![1530685086176](picture/1530685086176.png)

### 总结

exist 就感觉是in的变种（参看case 3）

in 后面实实在在返回数据，exist 则是返回ture 或者false（参看case 2）

## order by

order by 用来给符合条件的记录排序，当然我们这儿主要说的是关于`order by` 的一些优化工作：



# 索引

## 是什么

官方对索引的定义是：索引（index）是帮助Mysql高效获取数据的数据结构，可以得到本质，**索引是数据结构**

你可以理解为“**排好序的快速查找数据结构**”

索引有两个特点：查找、和排好序，所以索引会影响我们的sql 语句where 和 order by 

一般来说索引本身也很大，不可能全部存储在内存中，因此索引往往以索引文件的形式存储在磁盘上

##为什么需要索引

索引的目的在于**提高查找效率**，可以类比字典

如果要查“mysql” 这个单词，我们肯定需要定位到m字母，然后从上往下找到y字母，再找剩下的sql。

如果没有索引，那么你可能需要a-z，如果我想找到java开头的单词呢？或者Oracle开头的单词呢？

  是不是觉得没有这个索引，这些事事无法完成的。

## 特殊说明

索引列不能是表达式的一部分，也不能是函数的参数！

## 基本操作

### 创建索引

```mysql
create index idx_user_name on user(name);
```

索引名字一般格式为 `idx_表名_字段名`, 例如`idx_user_name`

###查看索引

```mysql
show index from tableName;
```

结果：

![](picture\TIM图片20180312113918.png)



## 索引分类

### 单值索引

即一个索引只包含一个单个列，一个表可以有多个单列索引

类似如下只使用一个字段来建索引：

```mysql
create index idx_user_name on user(name);
```

###复合索引

类似如下使用两个或者多个字段来建索引：

```mysql
create index idx_user_nameEmail on user(name,email);
```

###前缀索引

对于BLOB、TEXT或者很长的VARCHAR类型的列，必须使用前缀索引，因为mysql不允许索引这些列的完整长度

创建一个前缀索引：

```sql
alter table addmedallog201803 ADD KEY (platformName(3));
```

**主要是在字段的后面加括号，再加上数字**

然后再show index的时候就会看见，Sub_part 的值为 3；

### 唯一索引

索引列的值必须唯一，但允许有空值

## 哪些情况需要创建索引

![1523156317857](picture/1523156317857.png)



# 知识点



### 性能分析

#### profile

show profile 和 show profiles 语句可以展示**当前会话**(退出session后,profiling重置为0) 中执行语句的资源使用情况.

Profiling 功能由MySQL会话变量 : profiling控制,默认是OFF.关闭状态.

```sql
show variables like "%profi%"
```

```sql
select  @@profiling
```

通过以上两个语句均可以查看 当前session是否打开了profile功能. 



打开 profiling 功能：

```
set profiling=1;
```

执行 show profile 和 show profiles 语句

# information_schema

information_schema 这个数据库保存了MySQL服务器所有数据库的信息，是数据库自带的一个数据库。如数据库名，数据库的表，表栏的数据类型与访问权限等。这个库可以说说是MySQL的**元数据**的数据库。其保存内如如下：

这台MySQL服务器上

1.到底有哪些数据库

2.各个数据库有哪些表

3.每张表的字段类型是什么

4.各个数据库要什么权限才能访问 .......



结构如下：

![1522746861815](picture/1522746861815.png)

在这个数据中有一些表，结构如下：

![1522746903190](picture/1522746903190.png)



# 使用经验

## 查看某个库有哪些表

```mysql
select TABLE_NAME from information_schema.`TABLES` where TABLE_SCHEMA='sevendoor_game'; 
```

因为 `information_schema` 库的 `TABLES` 表中存放了所有库的表元数据信息

## 删除某个库的所有表

```sql
SELECT concat('DROP TABLE IF EXISTS ', table_name, ';')
FROM information_schema.tables
WHERE table_schema = 'yzry_game_1_998';
```

这样可以生成一个批量处理的sql语句，你需要再运行一次这个结果集
就可以删除所有的表而不删除数据库了

# 命令

##drop（删除表和数据）

```mysql
drop table table_name;
```

删除表，就是啥都没了

## exit （退出）

## mysql（登录）

mysql -h127.0.0.1 -uroot -psd123456 -P端口    

1. -h：后面跟服务器   
2. -u：跟用户   
3. -p：跟密码
4. -P：跟端口号

## mysqldump （mysql 转储）

**命令行下具体用法如下： **

**mysqldump -u用戶名 -p密码 -d 数据库名 表名 > 脚本名;**

导出整个数据库结构和数据

```shell
mysqldump -h localhost -uroot -p123456 database > dump.sql
```



导出单个数据表结构和数据

```shell
mysqldump -h localhost -uroot -p123456  database table > dump.sql
```



导出整个数据库结构（不包含数据）

```shell
mysqldump -h localhost -uroot -p123456  -d database > dump.sql
```



导出单个数据表结构（不包含数据）

```shell
mysqldump -h localhost -uroot -p123456  -d database table > dump.sql
```



## source （导入sql文件）

```shell
source /data/server/yzry_mainland_inspection_998/sql/game_data.sql;
```



##truncate（删数据留表结构）

```mysql
truncate table xx;
```

删除表 XX 中的内容，但是**保留表结构**

作用：删除内容、释放空间但不删除定义。与drop不同的是,他只是清空表数据而已,不删除表结构

## 

# 执行计划

## 是什么

使用 `EXPLAIN` 关键字可以模拟mysql 查询优化器执行SQL 查询语句，从而知道mysql时候如何处理我们的sql语句的，分析我们的sql语句或者表结构是否存在性能瓶颈。

## 能干嘛

![1523156522021](picture/1523156522021.png)

## 怎么玩

使用 explain + sql 语句就行了

## 包含信息分析

使用mysql 的执行计划可以得到一些如下的信息

![1523156757208](picture/1523156757208.png)

下面我们来详细看看这些字段的内容：

### id

此 id 表示select 查询的序列化，包含一组数字，表示查询中执行select子句或操作表的顺序，总共有三种情况：

1，id相同，执行顺序由上到下

2，id完全不同，id值越大优先级越高，越先被执行

3，id部分相同，id值越大优先级越高，然后相同的就从上到下执行

### select_type

- ​		simple: 简单的 select （没有使用 union或子查询）
  - ​	primary: 最外层的 select。
    - ​union: 第二层，在select 之后使用了 union。
    - ​dependent union: union 语句中的第二个select，依赖于外部子查询
    - ​subquery: 子查询中的第一个 select
    - ​dependent subquery: 子查询中的第一个 subquery依赖于外部的子查询
    - ​derived: 派生表 select（from子句中的子查询）

### table

显示这一行的数据是关于哪张表的

### type

以下列出了各种不同类型的表连接，依次是从最好的到最差的：

SYSTEM > CONST > EQ_REF > REF > RANGE > INDEX > ALL（不仅仅是连接，单表查询也会有）

####system：

表只有一行记录（等于系统表）。这是 const 表连接类型的一个特例，平时不会出现，这个可以不必考虑

####const：

表示通过索引一次就找到了，const 用于比较primary key 或者unique 索引。因为只匹配一行数据，所以很快，如将主键置于where 列表中，mysql就能将该查询转为一个常量

如下 roleid 是这张表的主键，并且有索引，我们在where后面使用使用它作为一个条件，‘69055XXX’ 就是一个常量

![1523166563864](picture/1523166563864.png)



####eq_ref:

从该表中会有一行记录被读取出来以和从前一个表中读取出来的记录做联合。与const类型不同的是，这是最好的连接类型。它用在索引所有部 分都用于做连接并且这个索引是一个primary key 或 unique 类型。eq_ref可以用于在进行“=”做比较时检索字段。比较的值可以是固定值或者是表达式，表达示中可以使用表里的字段，它们在读表之前已经准备好了

####ref: 

非唯一性索引扫描，返回匹配某个值的所有行

本质上也是一种索引访问，它返回所有匹配某个单独值的行，然而

它可能会找到多个符合条件的行，所以他应该属于查找和扫描的混合体

该表中所有符合检索值的记录都会被取出来和从上一个表中取出来的记录作联合。ref用于连接程序使用键的最左前缀或者是该键不是 primary key 或 unique索引（换句话说，就是连接程序无法根据键值只取得一条记录）的情况。当根据键值只查询到少数几条匹配的记录时，这就是一个不错的连接类型。 ref还可以用于检索字段使用=操作符来比较的时候。以下的几个例子中，MySQL将使用 ref 来处理ref_table，和eq_ref的区别是-用到的索引是否唯一性

####range：

只检索戈丁范围的行，使用一个索引来选择行，key列显示使用了哪个索引

一般就是在你的where语句中出现 >、>=、<、<=、is null、<=>、between、and 或 in 等的查询

这种**范围扫描索引**扫描比全表扫描要好，因为它需要开始于缩印的某一点，而结束于另一点，不用扫描全部索引

####index：

Full Index Scan, index 与 All 的区别为 index 只遍历索引树，这通常比All快，因为索引畏惧通常比数据文件小。

MySQL在查询的字段只是单独的索引的一部分的情况下使用这种连接类型



####all: 

Full Table Scan, 将遍历全表以找到匹配的行

**备注：一般来说，得保证查询至少达到range级别，最好能达到ref**

### possible_keys

显示可能应用在这张表（也可能是这次查询）的索引，一个或者多个，查询涉及到的字段上若存在索引，则该索引将被列出，**但不一定被查询实际使用**

### key

实际在此次sql中使用的索引。如果为NULL，则没有使用索引，

查询中若使用了覆盖索引，则该索引和查询的select字段重叠

#### 覆盖索引

复合索引使用了col1，和col2建了一个索引，我们在查询时，查询的字段也是col1，和col2，并且连顺序都是一样的，这就是覆盖索引

![1523275519775](picture/1523275519775.png)

### key_len

![1523275656260](picture/1523275656260.png)

![1523275669651](picture/1523275669651.png)

为了获得更高的精度，就必须付出更多的空间

### ref

![1526833729354](picture/1526833729354.png)

上面圈出来，表示 test库的t1表的ID字段

![1526833810767](picture/1526833810767.png)

### rows

根据表统计信息及索引选用情况，大致估算出找到所需的记录所需读取的行数，所以这个行数越少越好。

![1526833837743](picture/1526833837743.png)

### extra

在 extra 中会出现一些词语，我们来依依的看看这些词语

#### using filesort 

叫文件内排序，如果在extra 中出现了这个短语，说明mysql会对数据使用一个外部的索引排序，而不是按照表内的索引顺序进行读取。Mysql中无法利用索引完成的排序叫'文件排序'

![1528698850423](picture/1528698850423.png)

在sql语句后面加 \G 就是采用竖版的方式来展示结果。

继续来看 using filesort ，好好看看下面的例子：

![1528698923647](picture/1528698923647.png)

使用上面的方式就会发生文件排序，但是下面的sql语句就不会发生。

#### using temporary

使用了临时表

使用临时表保存中间结果，MySql 在对查询结果排序时使用临时表。常见于排序order by 和 分组查询 group by。

![1528699137064](picture/1528699137064.png)

都是建议遵守创建索引的字段个数和顺序

#### using index

使用索引：是好事

>  表示相应的select 操作中使用了覆盖索引，避免访问了表的数据行，效率不错！
>
>  如果同时出现了 using where，表明索引被用来执行索引键值的查找
>
>  如果没有同时出现 using where，表明索引用来读取数据而非执行查找动作。

![1528709492368](picture/1528709492368.png)

using index + using where ：表示用了索引，但是还是使用了查找

只有using index：表示直接根据索引就找到数据了，没有索引查找了

注意：虽然说覆盖索引有一些好处，但是如果将所有的字段一起做索引会导致索引文件过大，查询效率降低

#### using where

表明使用了where 过滤，可以结合上面的using index 中的内容一起理解

#### using join buffer

使用了连接缓存

可以考虑join buffer 配大一点

#### impossible where

where 子句的值总是false，不能用来获取任何元组

![1528709743316](picture/1528709743316.png)

一个人的名字不可能既是july 又是z3

#### select tables optimized away

在没有 Group by 子句的情况下，基于索引优化min/max 操作或者对于 MyISAM 存储引擎优化 COUNT（*）操作，不必等到执行阶段在进行计算，查询执行计划生成的阶段即完成优化。

**这个用的不多**

#### distinct

优化 distinct 操作，在找到第一匹配的元组后即停止找同样值的动作。

## 看看一个实际的操作

![1528710087470](picture/1528710087470.png)

![1528710105877](picture/1528710105877.png)

# 事务

先来看下百度百科对数据库事务的定义：

　　作为单个逻辑单元执行一系列操作，要么完全执行，要么完全不执行。事务处理可以确保除非事务性单元内的所有操作都成功完成，否则不会永久更新面向数据的资源。

## 事务特性

**事务有四个属性，称为ACID属性：**

1、原子性(Atomicity):事务是一个原子单位，要么全部执行，要么全部不执行。

2、一致性(Consistent):事务的开始和结束，数据都必须保持一致状态。

3、隔离性(isolation):数据库系统提供隔离机制，保证并发事务之间是互相不干扰的。也就意味着事务处理过程中的中间状态对其他的事务是透明的。

4、持久性(Durable):事务完成之后，对数据的修改是永久性的，即使出现系统故障也能够保持。

## 数据库事务问题

**1. 脏读  (A事务读了B事务没有提交的修改)**

事务T1修改某个字段的值，然后事务T2读取该值，此后T1撤销了对该字段的更新，或者更新成另外的值才commit到数据库中，这样T2读取的数据是无效的或者错误的。导致T2依据脏数据所做的操作也是错误的。

> 思聪同学中午去食堂吃饭，看到窗边的座位被如花同学占有了，思聪认为这个座位已经被占有了，就转身去找其他的座位。不料，如花同学起身离开了。事实是：如花并不是吃饭，而是临时坐在那里等她的约会对象，只是临时小坐一会，并没有真正“commit”。

**2. 不可重复读  (同一条数据A事务多次验证结果不一样，在两次验证过程了，B事务修改并提交了这个数据)**

在数据库访问中，一个事务范围内的两次相同的查询却返回了不同的数据。

事务T1读取某一数据，事务T2读取并修改了该数据，T1为了对读取值进行验证而重新读取，却发现得到了不同的结果。

> 思聪同学中午去食堂吃饭，看到窗边的座位是空的，便屁颠屁颠的跑去打饭，回来后却发现这个座位被如花同学抢去了。

**3. 幻读  ()**

幻读解决了不可重复读的问题，即在同一个事务范围内，两次相同的查询结果是相同的。但是可以新增表中的数据记录。

幻读是指事务T1对表中的数据进行修改，假设修改涉及了表中全部的数据行，同时第二个事务也修改这个表中的数据，这种修改是向表中插入一条新的数据。后面就会出现操作了T1事务的用户发现表中还有没有修改的数据行，仿佛出现了幻觉一样。

> 思聪同学中午去食堂吃饭，看到窗边的座位是空的，便屁颠屁颠的跑去打饭，回来后窗边的座位还是空的，便很高兴坐上去准备开始吃饭，这时候却发现如花同学搬了一个小板凳坐在旁边狼吞虎咽，思聪顿时没有了胃口。



如果需要解决脏读、不可重复读、幻读等这些数据库读现象，就必须相应提高事务的隔离级别。但是数据库的隔离级别越高，对应的并发能力就越弱，性能也就相应的越差，所以我们还需根据具体的应用场景去权衡。

## 数据库隔离级别

### **1、读未提交**

事务的最低隔离级别，在这种隔离级别下，一个事务可以读取另外一个事务未提交的数据。

**数据库锁实现原理：**

**事务T在读数据的时候并未对数据进行加锁，事务T在修改数据的时候对数据增加行级共享锁**

T1在读取数据时，T2可以对相同数据进行读取、修改。因为T1没有进行任何锁操作；当T2对记录进行修改时，T1再次读取数据可以读取到T2修改后的数据。因为T2对数据进行修改只增加了行级共享锁，T1可以再增加共享读锁进行数据读取（尽管T2没有提交事务）

如上所述，这种隔离级别，会导致脏读现象

### **2、读已提交**

在一个事务修改数据过程中，如果事务没有进行提交，其他事务不能读取该数据

**数据库锁实现原理：**

**事务T在读取数据时增加行级共享锁，读取一旦结束，立即释放；事务T在修改数据时增加行级排他锁，直到事务结束才释放。**

T1在读取数据的过程中，T2也可以对相同数据进行读取，但是不能进行修改（T1增加的是共享锁，T2也可以增加共享锁，但是不能增加排他锁）。T1读取结束后，会立即释放共享锁，这时T2可以增加排他锁，对数据进行修改，而此时T1既不能对数据进行读取也不能进行修改，直到T2事务结束。

如上所述，这种隔离级别，解决了脏读问题，但是不能解决不可重复读现象。

### **3、可重复读**

**事务T在数据读取时，必须增加行级共享锁，直到事务结束；事务T在修改数据过程中，必须增加行级排他锁，直到数据结束。**

**数据库锁实现原理：**

T1在读取数据的过程中，T2也可以对相同数据进行读取，但是不能进行修改（T1增加的是共享锁，T2也可以增加共享锁，但是不能增加排他锁）。直到T1事务结束后，才会释放共享锁，这时T2才可以增加排他锁，对数据进行修改。

如上所述，这种隔离级别，解决了不可重复读现象，但是这种隔离级别解决不了幻读的问题：

T1进行查询，读取了10条记录，并对十条记录增加了行级锁，此时T2是无法对这10行数据进行修改操作的，但是由于没有表级锁，它可以增加一条满足T1查询条件的记录。随后T1在进行查询时，会发现虽然10条记录没有改变，但是突然多了一条记录。

### **4、序列化**

产生幻读是由于没有进行范围查询时没有增加范围锁。

**数据库锁实现原理：**

** 事务T在读取数据时，必须先增加表级共享锁，直到事务结束才释放；事务T在修改数据时，必须先增加表级排他锁，直到事务结束才释放。**

T1在读取A表时，增加了表级共享锁，此时T2也可以读取A表，但是不能进行任何数据的修改，直到T1事务结束。随后T2可以增加对A表的表级排他锁，此时T1不能读取A表中的任何数据，更不能进行修改。

如上所述，可序列化解决了脏读、不可重复读、幻读等读现象，但是隔离级别越来越高的同时，在并发性上也就越来越低。

# 日志

MySQL中有六种日志文件，分别是：

1. 重做日志（redo log）
2. 回滚日志（undo log）
3. 二进制日志（binlog）
4. 错误日志（errorlog）
5. 慢查询日志（slow query log）
6. 一般查询日志（general log），中继日志（relay log）。

其中重做日志和回滚日志与事务操作息息相关，二进制日志也与事务操作有一定的关系，这三种日志，对理解MySQL中的事务操作有着重要的意义。



**重做日志（redo log）**

**作用：**

确保事务的持久性。

防止在发生故障的时间点，尚有脏页未写入磁盘，在重启mysql服务的时候，根据redo log进行重做，从而达到事务的持久性这一特性。

**内容：**

物理格式的日志，记录的是物理数据页面的修改的信息，其redo log是顺序写入redo log file的物理文件中去的。

**什么时候产生：**

事务开始之后就产生redo log，redo log的落盘并不是随着事务的提交才写入的，而是在事务的执行过程中，便开始写入redo log文件中。

**什么时候释放：**

当对应事务的脏页写入到磁盘之后，redo log的使命也就完成了，重做日志占用的空间就可以重用（被覆盖）。

**对应的物理文件：**

默认情况下，对应的物理文件位于数据库的data目录下的ib_logfile1&ib_logfile2

innodb_log_group_home_dir 指定日志文件组所在的路径，默认./ ，表示在数据库的数据目录下。

innodb_log_files_in_group 指定重做日志文件组中文件的数量，默认2

关于文件的大小和数量，由一下两个参数配置

innodb_log_file_size 重做日志文件的大小。

innodb_mirrored_log_groups 指定了日志镜像文件组的数量，默认1

**其他：**

很重要一点，redo log是什么时候写盘的？前面说了是在事物开始之后逐步写盘的。

之所以说重做日志是在事务开始之后逐步写入重做日志文件，而不一定是事务提交才写入重做日志缓存，

原因就是，重做日志有一个缓存区Innodb_log_buffer，Innodb_log_buffer的默认大小为8M(这里设置的16M),Innodb存储引擎先将重做日志写入innodb_log_buffer中。

![img](https://mmbiz.qpic.cn/mmbiz_png/eZzl4LXykQxJL2PDCnLH4nH6cmCTy6dCm3zOIfic3RyRGFibVh2ogVxxlCGnt6Bt8fUDOTJTVLhVXBlKzibGaUuuw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

然后会通过以下三种方式将innodb日志缓冲区的日志刷新到磁盘

1，Master Thread 每秒一次执行刷新Innodb_log_buffer到重做日志文件。

2，每个事务提交时会将重做日志刷新到重做日志文件。

3，当重做日志缓存可用空间 少于一半时，重做日志缓存被刷新到重做日志文件

由此可以看出，重做日志通过不止一种方式写入到磁盘，尤其是对于第一种方式，Innodb_log_buffer到重做日志文件是Master Thread线程的定时任务。

因此重做日志的写盘，并不一定是随着事务的提交才写入重做日志文件的，而是随着事务的开始，逐步开始的。

另外引用《MySQL技术内幕 Innodb 存储引擎》（page37）上的原话：

即使某个事务还没有提交，Innodb存储引擎仍然每秒会将重做日志缓存刷新到重做日志文件。

这一点是必须要知道的，因为这可以很好地解释再大的事务的提交（commit）的时间也是很短暂的。

**回滚日志（undo log）**

**作用：**

保存了事务发生之前的数据的一个版本，可以用于回滚，同时可以提供多版本并发控制下的读（MVCC），也即非锁定读

**内容：**

逻辑格式的日志，在执行undo的时候，仅仅是将数据从逻辑上恢复至事务之前的状态，而不是从物理页面上操作实现的，这一点是不同于redo log的。

**什么时候产生：**

事务开始之前，将当前是的版本生成undo log，undo 也会产生 redo 来保证undo log的可靠性

**什么时候释放：**

当事务提交之后，undo log并不能立马被删除，

而是放入待清理的链表，由purge线程判断是否由其他事务在使用undo段中表的上一个事务之前的版本信息，决定是否可以清理undo log的日志空间。

**对应的物理文件：**

MySQL5.6之前，undo表空间位于共享表空间的回滚段中，共享表空间的默认的名称是ibdata，位于数据文件目录中。

MySQL5.6之后，undo表空间可以配置成独立的文件，但是提前需要在配置文件中配置，完成数据库初始化后生效且不可改变undo log文件的个数

如果初始化数据库之前没有进行相关配置，那么就无法配置成独立的表空间了。

关于MySQL5.7之后的独立undo 表空间配置参数如下

innodb_undo_directory = /data/undospace/ –undo独立表空间的存放目录

innodb_undo_logs = 128 –回滚段为128KB

innodb_undo_tablespaces = 4 –指定有4个undo log文件

如果undo使用的共享表空间，这个共享表空间中又不仅仅是存储了undo的信息，共享表空间的默认为与MySQL的数据目录下面，其属性由参数innodb_data_file_path配置。

![img](https://mmbiz.qpic.cn/mmbiz_png/eZzl4LXykQxJL2PDCnLH4nH6cmCTy6dCd5b2Hc3K0wEKjeibVOzjBT8vJXCDfTfPZ0Fd1r90beXKiaqS1JqwErng/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

**其他：**

undo是在事务开始之前保存的被修改数据的一个版本，产生undo日志的时候，同样会伴随类似于保护事务持久化机制的redolog的产生。

默认情况下undo文件是保持在共享表空间的，也即ibdatafile文件中，当数据库中发生一些大的事务性操作的时候，要生成大量的undo信息，全部保存在共享表空间中的。

因此共享表空间可能会变的很大，默认情况下，也就是undo 日志使用共享表空间的时候，被“撑大”的共享表空间是不会也不能自动收缩的。

因此，mysql5.7之后的“独立undo 表空间”的配置就显得很有必要了。

## 二进制日志（binlog）:

binlog日志用于记录所有更新且提交了数据或者已经潜在更新提交了数据（例如，没有匹配任何行的一个DELETE）的所有语句。语句以“事件”的形式保存，它描述数据更改。

### 特点

1. binlog是MySQL Server层记录的日志
2. ​

### 作用：

1. 用于复制，在主从复制中，从库利用主库上的binlog进行重播，实现主从同步。
2. 用于数据库的基于时间点的还原。

### 内容：

逻辑格式的日志，可以简单认为就是执行过的事务中的sql语句。

但又不完全是sql语句这么简单，而是包括了执行的sql语句（增删改）反向的信息，

也就意味着delete对应着delete本身和其反向的insert；update对应着update执行前后的版本的信息；insert对应着delete和insert本身的信息。

在使用mysqlbinlog解析binlog之后一些都会真相大白。

因此可以基于binlog做到类似于oracle的闪回功能，其实都是依赖于binlog中的日志记录。

**什么时候产生：**

事务提交的时候，一次性将事务中的sql语句（一个事物可能对应多个sql语句）按照一定的格式记录到binlog中。

这里与redo log很明显的差异就是redo log并不一定是在事务提交的时候刷新到磁盘，redo log是在事务开始之后就开始逐步写入磁盘。

因此对于事务的提交，即便是较大的事务，提交（commit）都是很快的，但是在开启了bin_log的情况下，对于较大事务的提交，可能会变得比较慢一些。

这是因为binlog是在事务提交的时候一次性写入的造成的，这些可以通过测试验证。

**什么时候释放：**

binlog的默认是保持时间由参数expire_logs_days配置，也就是说对于非活动的日志文件，在生成时间超过expire_logs_days配置的天数之后，会被自动删除。

![img](https://mmbiz.qpic.cn/mmbiz_png/eZzl4LXykQxJL2PDCnLH4nH6cmCTy6dC8DUGeoL0fjSZb9DydKY9bK4U3ea2zjHmWdKib7LCicyu7z6bu2UDxqxA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

**对应的物理文件：**

配置文件的路径为log_bin_basename，binlog日志文件按照指定大小，当日志文件达到指定的最大的大小之后，进行滚动更新，生成新的日志文件。

对于每个binlog日志文件，通过一个统一的index文件来组织。

![img](https://mmbiz.qpic.cn/mmbiz_png/eZzl4LXykQxJL2PDCnLH4nH6cmCTy6dCLuerlK9Y5pHIN3EXSLIWoyEAjQospvHBG8kTK2G9ebiaV92vDNbZ4Nw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

### 查看binlog事件

```sql
SHOW BINLOG EVENTS IN 'logbin.000001'
```



**其他：**

二进制日志的作用之一是还原数据库的，这与redo log很类似，很多人混淆过，但是两者有本质的不同

1. 作用不同：redo log是保证事务的持久性的，是事务层面的，binlog作为还原的功能，是数据库层面的（当然也可以精确到事务层面的），虽然都有还原的意思，但是其保护数据的层次是不一样的。
2. 内容不同：redo log是物理日志，是数据页面的修改之后的物理记录，binlog是逻辑日志，可以简单认为记录的就是sql语句
3. 另外，两者日志产生的时间，可以释放的时间，在可释放的情况下清理机制，都是完全不同的。
4. 恢复数据时候的效率，基于物理日志的redo log恢复数据的效率要高于语句逻辑日志的binlog

关于事务提交时，redo log和binlog的写入顺序，为了保证主从复制时候的主从一致（当然也包括使用binlog进行基于时间点还原的情况），是要严格一致的，

MySQL通过两阶段提交过程来完成事务的一致性的，也即redo log和binlog的一致性的，理论上是先写redo log，再写binlog，两个日志都提交成功（刷入磁盘），事务才算真正的完成。

# 索引优化

## 索引分析

### 单表

有个需求是：

查询`category_id` 为 1且comments 大于1的情况下，views最多的`article_id`。

1.0 在没有使用索引的情况下：

![1528718182798](picture/1528718182798.png)

1.1建一个包含三个字段的符合索引

![1528718206041](picture/1528718206041.png)

![1528718232219](picture/1528718232219.png)

![1528718249660](picture/1528718249660.png)

![1528718259866](picture/1528718259866.png)

#### 总结

就是要用到索引，而且基于最合适的复合索引，不要出现了using filesort等现象

### 双表

![1528717556766](picture/1528717556766.png)

所以**左连接在右表关联的字段上加索引**，

在不能变索引的情况下，就可以交换表的位置，让左表变右表

同理，**右连接在左表关联的字段上加索引**

### 三表

先来看看连接三张表的sql语句：

```sql
SELECT * FROM class LEFT JOIN book ON class.card=book.card LEFT JOIN phone ON book.card = phone.card;
```

![1528717825652](picture/1528717825652.png)

其实就是上面两表情况的复杂版，先看前面的两个表class 和 book ，先在book 的card 字段建立索引，然后在phone 表的 card 字段建索引。

### Join 语句的优化

![1528717930731](picture/1528717930731.png)

## 索引失效（应该避免）

后面的索引失效的例子讲解都是基于此SQL 创建的表来的：

![1528717132749](picture/1528717132749.png)

### 1.全值匹配我最爱

我们使用`name`、`age`、`pos` 三个字段建了一个复合索引，如下的三种方式都能使用到索引，而且也建议按照建索引的顺序来使用。

![1528648734265](picture/1528648734265.png)

对于上面的例子，我们也可以使用 

```sql
where name='July' AND pos = dev AND age = 25;
```

也会使用到索引，因为mysql的优化器会自己做调整

### 2.最佳左前缀法则

如果索引了多列，要遵守最左前缀法则，指的是查询从索引的最左前列开始并且**不跳过索引中的列**。

**大哥不能死，中间兄弟不能断**

![1528627800463](picture/1528627800463.png)

### 3.不在索引列上做任何操作（计算，函数，（自动or手动）类型转换）

会导致所以索引失效而转向全表扫描

![1528716628090](picture/1528716628090.png)

### 4.存储引擎不能使用索引中范围条件右边的列

![1528716717915](picture/1528716717915.png)

表中的符合索引的顺序是 name，age，pos， 但是最后一条的sql，中间的条件是一个范围，不是一个具体的值。

### 5.尽量使用覆盖索引(只访问索引的查询（索引列和查询列一致）)，减少select *

![1528716893294](picture/1528716893294.png)



### 6.不等于(!= 或者 <>)

![1528643863497](picture/1528643863497.png)

### 7.is null, is not null 让索引失效

![1528644084394](picture/1528644084394.png)



### 8.like 查询 select * 使用 ‘%abc’ 会让索引失效

![1528644319796](picture/1528644319796.png)



如果两边都是% 即‘%july%’ 这种类似的，那么可以考虑使用覆盖索引，而不要总是使用 select * 

### 9.字符串不加单引号

![1528647074983](picture/1528647074983.png)

这两条SQL在执行时都能查出数据，但是呢，第二条是mysql底层自己隐式做的类型转换，与上面说的第三条有共通之处。

### 10.少用or，用它来连接时会使索引失效

![1528647905685](picture/1528647905685.png)

## 索引优化一般性建议

![1528725546090](picture/1528725546090.png)

对于like后面的表达式是常亮开头的就可以使用到这个字段

# 查询截取分析

## 查询优化

### 小表驱动大表

### order by 关键字排序



# Mysql优化

## 连接数

### 查看连接数：

```sql
show variables like 'max_connections';(查可以看当前的最大连接数)
```

### 修改连接数：

方法一：

```sql
set global max_connections=1000;(设置最大连接数为1000，可以再次查看是否设置成功)
```

方法二：

通过修改配置文件来修改mysql最大连接数(max_connections)

这种方式说来很简单，只要修改MySQL配置文件my.ini 或 my.cnf的参数max_connections，将其改为max_connections=1000，然后重启MySQL即可。

### 查看连接：

```sql
show processlist; 
```

如果是root帐号，你能看到所有用户的当前连接。如果是其它普通帐号，只能看到自己占用的连接。

show processlist;只列出前100条，如果想全列出请使用：

```sql
show full processlist;
```

