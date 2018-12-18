# Maven 基础说明

## pom内置属性

 ${project.build.sourceDirectory}:项目的主源码目录，默认为src/main/java/.                                                                                                          ​${project.build.testSourceDirectory}:项目的测试源码目录，默认为/src/test/java/.                                                                                                ${project.build.directory}:项目构建输出目录，默认为target/.                                                                                                                                ​${project.build.outputDirectory}:项目主代码编译输出目录，默认为target/classes/.                                                                                              ${project.build.testOutputDirectory}:项目测试代码编译输出目录，默认为target/testclasses/.                                                                                ​${project.groupId}:项目的groupId.                                                                                                                                                                        ${project.artifactId}:项目的artifactId.                                                                                                                                                                      ​${project.version}:项目的version,于${version}等价                                                                                                                                                ​${project.build.finalName}:项目打包输出文件的名称，默认为${project.artifactId}​${project.version}.

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

#### <optional>

一般的值是 true 如下：

```xml
<dependency>
  <groupId>sample.ProjectD</groupId>
  <artifactId>ProjectD</artifactId>
  <version>1.0-SNAPSHOT</version>
  <optional>true</optional>
</dependency>
```

当project-A 依赖project-B,  project-B 依赖project-D时，

当project-B的<optional>true</optional>时, project-A中如果没有显式的引入project-D, 则project-A不依赖project-D, 即project-A可以自己选择是否依赖project-D。

默认<optional>的值为false, 及子项目必须依赖

#### <exclusion>

<exclusion> 只是 <exclusions> 的一个子标签，用来排除传递依赖，或者用来处理jar冲突

```xml
<dependency>
  <groupId>sample.ProjectB</groupId>
  <artifactId>Project-B</artifactId>
  <version>1.0</version>
  <scope>compile</scope>
  <exclusions>
    <exclusion> 
      <groupId>sample.ProjectC</groupId>
      <artifactId>Project-C</artifactId>
    </exclusion>
  </exclusions> 
</dependency>
```



# 插件

## 常用插件

### maven-resources-plugin

暂时先参看这篇文章：

[点这里](https://www.aliyun.com/jiaocheng/790173.html)

# 打包

## 打成可执行jar

1，配置打包的方式：

```xml

```



