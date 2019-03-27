# Mongodb Helloworld

## 安装

## 启动

启动可以直接在mongod 命令后面直接加参数，但是很不方便，如下：



也可以使用配置文件的方式，实际情况，更推荐这种方式：

```shell
mongod -f config/mongodb.conf
```

mongodb.conf

```properties
dbpath= /smapp/servers/mongoDB/data/
logpath=/smapp/servers/mongoDB/logs/mongoDB-logs.log
logappend=true
replSet=smzc
bind_ip=0.0.0.0
port=27017
oplogSize=10000
fork=true
noprealloc=false
```

