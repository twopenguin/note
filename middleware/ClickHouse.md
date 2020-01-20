

## 表

### 创建表

Buffer

Buffers 将数据写入到内存中，周期性刷新数据到另外的表中。在读取操作的过程中，数据从 Buffer 和另外的表中同时读取。

Buffer(database, table, num_layers, min_time, max_time, min_rows, max_rows, min_bytes, max_bytes) 引擎参数：database, table - 表刷新数据。与数据库名称不同，你能够使用常量表达式返回字符串。

num_layers - 并行水平。

物理上看，表能够用独立 buffers 的‘num_layers’代表。默认值为16，min_time, max_time, min_rows, max_rows, min_bytes，同时max_bytes是从 buffer 刷新数据的条件。

数据从buffer 中刷新，写入到目标表，如果所有的‘min’条件或者最少一个‘max’条件组合时。

min_time, max_time - 从第一次写入到 buffer 的时间。

min_rows, max_rows - 在 buffer 中的行数。

min_bytes, max_bytes - 在 buffer 中字节的数量。

在写入操作的过程中，数据写入到一个‘num_layers’数的随机 buffers 中。或者，如果插入的数据部分足够大(大于 ‘max_rows’ 或者 ‘max_bytes’),它被直接写入到目标表，忽略此 buffer。

刷新数据被单独计算为每个‘num_layers’buffers。例如，如果num_layers = 16 and max_bytes = 100000000，最大的内存消耗是1.6 GB。

例如：

CREATE TABLE merge.hits_buffer AS merge.hits ENGINE = Buffer(merge, hits, 16, 10, 100, 10000, 1000000, 10000000, 100000000)

## 视图

查看视图`show tables;`

删除视图`drop table if exists xxxx`

### 普通视图



### 物化视图

模板：

```sql
CREATE MATERIALIZED VIEW {视图名} {字段}ENGINE = {表引擎}() 
```



```sql
CREATE MATERIALIZED VIEW app.study_videolog_apprea_view (`media_id` UInt64, `st` UInt64, `u_i` String, `d_i` String, `tk_id` String, `time` DateTime, `cat` String, `act` String, `e_t` String, `c_p` String) ENGINE = MergeTree() ORDER BY (st) POPULATE AS sql的select语句
```







## 困惑的地方

有些视图的前面有.inner.这样的标识，比如`.inner.study_livelog_daily_view`

