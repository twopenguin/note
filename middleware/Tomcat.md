# Tomcat 结构

## Server

Tomcat中最顶层的容器叫Server，代表整个服务器。

一个Tomcat中只有一个Server，一个Server可以包含多个Service

Tomcat 里的Server 由 `org.apache.catalina.startup.Catalina` 来管理，Catalina是整个Tomcat的管理类，它里面的三个方法，load、start、stop分别来管理整个服务器的生命周期，

### Service

Service 主要包含两部分：Connector 和 Container

一个Service 只有一个Container ，但可以有多个Connector  （因为一个服务可以有多个连接，如同时提供http 和 https 连接，也可以提供相同协议不同端口的连接）

#### Container

Container用于封装和管理Servlet，以及处理request请求。

##### Engine

##### Host

##### Context

##### Wrapper

#### Connector 

Connector 用于处理连接相关的事情，并提供Socket 与 request 、response的转换

#### Connector

### Service

### Service

