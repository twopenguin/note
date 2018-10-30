# 概念

## 正向代理

代理的是客户端

比如我们要在国内访问google.com ，我们使用VPN来访问，我们的客户端对于google.com来说是透明的，这个时候，这个VPN，就是代理的是我们的客户端，这就是正向代理.

## 反向代理

反向代理，代理的是服务端，对于客户端来说，具体是哪个服务器处理的我的请求是未知的

## 常用的web服务器介绍

tomcat、weblogic、jetty、nginx、iis、jboss、websphere、glassfish、resin

动态web服务器和静态Web服务器

apache：静态web服务器

tomcat：动态servlet服务器



./nginx -t 可以验证配置文件有没有错误

#配置

## 日志

### 日志切割

nginx没有自己提供按天或者月自动切割access.log 的功能

使用crontab来定时执行下面的脚本

我们需要使用下面的方式自己来进行日志切割：

1.mv access.log access.log.${date}

2.kill -USER1 Nginx 进程号 ： 让nginx 重新生成一个日志文件access.log

## location的语法和规则



