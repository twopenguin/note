# 配置说明

## pom.xml

### <dependency>

此标签用来配置程序的依赖项，格式如下：

```xml
<dependency>
  <groupId>com.google.guava</groupId>
  <artifactId>guava</artifactId>
  <version>17.0</version>
  <type>pom</type>
</dependency>
```

接下来说说几个子节点

对于<groupId>、<artifactId>、<version>，这几个节点不必多说，主要看看<type> 节点

#### <type>

type节点的几种取值说明：

dependency为什么会有type为pom，默认的值是什么？ 

dependency中type默认为jar即引入一个特定的jar包。那么为什么还会有type为pom呢?当我们需要引入很多jar包的时候会导致pom.xml过大，我们可以想到的一种解决方案是定义一个父项目，但是父项目只有一个，也有可能导致父项目的pom.xml文件过大。这个时候我们引进来一个type为pom，意味着我们可以将所有的jar包打包成一个pom，然后我们依赖了pom，即可以下载下来所有依赖的jar包

# 插件

## 常用插件

### maven-resources-plugin

暂时先参看这篇文章：

[点这里](https://www.aliyun.com/jiaocheng/790173.html)

