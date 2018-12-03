# 简介

Elasticsearch也使用Java开发并使用Lucene作为其核心来实现所有索引和搜索的功能，但是它的目的是通过简单的`RESTful API`来隐藏Lucene的复杂性，从而让全文搜索变得简单。

不过，Elasticsearch不仅仅是Lucene和全文搜索，我们还能这样去描述它：

- 分布式的实时文件存储，每个字段都被索引并可被搜索
- 分布式的实时分析搜索引擎
- 可以扩展到上百台服务器，处理PB级结构化或非结构化数据


# 安装

## Windows

### 分布式

在windows下面开了多个cmd窗口的时候，为了方便识别哪个是master， 哪个是slave，可以修改如下文件：

`elasticsearch-6.5.1-master\bin\elasticsearch.bat`

```vb
@echo off

setlocal enabledelayedexpansion
setlocal enableextensions

TITLE ES-Master ##这儿即为添加项目

SET params='%*'
```




# 集群探索

## 集群健康

使用下面的url查看

```
/_cat/health?v
```



列出所有的索引：

```
localhost:9200/_cat/indices?v
```



## Create an Index

Now let’s create an index named "customer" and then list all the indexes again:

```
PUT /customer?pretty
GET /_cat/indices?v
```



The first command creates the index named "customer" using the PUT verb. We simply append `pretty` to the end of the call to tell it to pretty-print the JSON response (if any).

And the response:

```
health status index    uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   customer 95SQ4TSUT7mWBT7VNHH67A   5   1          0            0       260b           260b
```



## Index and Query a Document

Let’s now put something into our customer index. We’ll index a simple customer document into the customer index, with an ID of 1 as follows:

```
PUT /customer/_doc/1?pretty
{
  "name": "John Doe"
}
```



And the response:

```
{
  "_index" : "customer",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "result" : "created",
  "_shards" : {
    "total" : 2,
    "successful" : 1,
    "failed" : 0
  },
  "_seq_no" : 0,
  "_primary_term" : 1
}
```

From the above, we can see that a new customer document was successfully created inside the customer index. The document also has an internal id of 1 which we specified at index time.

It is important to note that Elasticsearch does not require you to explicitly create an index first before you can index documents into it. In the previous example, Elasticsearch will automatically create the customer index if it didn’t already exist beforehand.

Let’s now retrieve that document that we just indexed:

```
GET /customer/_doc/1?pretty
```



And the response:

```
{
  "_index" : "customer",
  "_type" : "_doc",
  "_id" : "1",
  "_version" : 1,
  "found" : true,
  "_source" : { "name": "John Doe" }
}
```

Nothing out of the ordinary here other than a field, `found`, stating that we found a document with the requested ID 1 and another field, `_source`, which returns the full JSON document that we indexed from the previous step.

