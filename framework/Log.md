#日志组件总体介绍

Java 生态中，有很多的日志组件，比如log4j、slf4j、logback等等。下面的这篇文章就是我理清楚这些组件之间的关系，以前他们之间差异的笔记。

Java的日志组件分为两类一类是日志门面（比如`common-logging`），可以理解为接口，一类是日志功能的实现组件（比如`log4j2`）,类似实现类。下面列举常见的日志组件：



#门面组件

## JCL（Jakarta Commons Logging）

其实就是 `Apache Commons Logging`， 引入包如下：

```xml
<!-- https://mvnrepository.com/artifact/commons-logging/commons-logging -->
<dependency>
    <groupId>commons-logging</groupId>
    <artifactId>commons-logging</artifactId>
    <version>1.2</version>
</dependency>
```



##SLF4j （Simple Logging Facade for Java ）



#实现组件

## JUL（java.util.logging）

## Log4j

## Log4j2

## Logback





