# 概念和简介

什么是容器：

一种虚拟化方案

## 安装

### Windows 安装

#### 遇到的问题

问题一：

VBoxManage.exe error: The virtual machine 'default' has terminated unexpectedly

解决步骤：我卸载了老的virtualBox， 安装了一个更新的



问题二：

looks like something went wrong in step ‘looking for vboxmanage.exe’

网上是这么说的：

Docker 找不到 vboxmanage.exe，查看 Docker 安装目录下的 `start.sh`，发现以下代码片段：

```shell
STEP="Looking for vboxmanage.exe"
if [ ! -z "$VBOX_MSI_INSTALL_PATH" ]; then
  VBOXMANAGE="${VBOX_MSI_INSTALL_PATH}VBoxManage.exe"
else
  VBOXMANAGE="${VBOX_INSTALL_PATH}VBoxManage.exe"
fi
```

我尝试着在windows的系统变量中加入了这些环境变量，但是还是不行

我的解决办法：

重新安装virtualBox，并且按照默认路径来安装



## 须知

### 如何查一个命令怎么用

比如`docker run`命令：可以使用`man docker-run`来查看其使用方法

就是在命令之间加一个`-`



# Docker 核心组件

## Docker客户端和服务器

## Docker镜像

### 相关命令

#### docker pull 

可以使用`docker pull` 命令从网络上下载镜像，改命令的格式为

```dockerfile
docker pull NAME[:TAG]
```

对于docker 镜像来说，若干不显示的指定TAG，则默认会选择latest 标签，即下载仓库中最新版本的镜像。

#### docker images

使用`dockerimages` 命令可以列出本地主机上已有的镜像。



在列出信息中，可以看到几个字段信息：

1. 来自于哪个仓库
2. 镜像的标签信息，或者说版本
3. 镜像的ID号（唯一）
4. 创建时间
5. 镜像大小

#### docker inspect(查看镜像信息)

```shell
docker inspect 镜像的唯一id
```

此命令返回的是一个JSON格式的信息，如果只哟啊其中一项的内容，可以使用-f 命令。例如获取镜像的`Atchitecture`信息：

```shell
docker inspect -f {{".Arichitecture"}} 550
```

#### docker search(搜索远端镜像)



#### docker rmi（删除本地镜像）

### 创建镜像

有三种方式创建镜像：

基于已有镜像的容器创建，基于本地模板导入，基于Dockerfile创建。



## Registry

Docker 用 Registry 来保存用户构建的镜像。Registry 分为公共和是有两种。

## Docker容器

### 容器基本操作

#### 启动容器

命令格式为

```
docker run IMAGE [COMMAND] [ARGS]
```

后面的`[COMMAND] [ARGS]` 是在容器启动之后处理的命令和参数

这种启动方式一般是一次性的

我们也可以启动交互式的容器

#### 启动交互式的容器

```
docker run -i -t IMAGE /bin/bash
```

-i --interactive=true|false 默认是false

-t --tty = true|false 默认是false

退出这种交互是容器，用`exit`命令



#### 查看容器

```
docker ps [-a] [-l]
```

-a 指列出所有的容器，无论启动还是关闭的容器

-l 指最新创建的一个容器

如果不给`ps` 命令加任何的参数，就是指列出正在运行的容器，



#### 自定义容器名

```
docker run --name=自定义名字 -i -t IMAGE /bin/bash
```

其实就在前面的基础上面加上`--name=自定义名字` 这个参数就可以了



#### 重新启动已经停止的容器

```
docker start [-i] 容器名
```

-i ： 表示以交互的形式启动



#### 删除停止的容器

```
docker rm 容器名
```

不能删除正在运行的容器



### 守护式容器

#### 怎么启动守护式

在我们实际的生产环境中需要一些容器一直运行，就是希望它在后台一直运行，类似守护线程一样的道理

方法一：

在我们以如下的命令启动一个交互式容器之后:

```
docker run -i -t IMAGE /bin/bash
```

以 `Ctrl+P`    和`Ctrl+Q` 来进入守护式，其实这样在后台就一直运行，这样，使用`docker ps`命令就可以查看到



方法二：

直接以后台的方式运行run命令

```
docker run -d IMAGE [COMMAND] [args]
```

-d：就是说以后台的方式启动容器

```shell
[root@iz2ze20vl8jnph0si0n7jsz ~]# docker run --name dc1 -d ubuntu /bin/bash -c "while true; do echo hello world;sleep 1;done"
282bc392c684c71e7da62a15976822359fd7a0c7312de705051a006322e112f2
[root@iz2ze20vl8jnph0si0n7jsz ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
282bc392c684        ubuntu              "/bin/bash -c 'while…"   4 seconds ago       Up 3 seconds                            dc1
```



如何再次进入这个容器呢

```
docker attach 容器名
```

这个容器名就是刚刚的那个进入了守护式的容器的名字



#### 查看容器的日志

```
docker logs [-f] [-t] [--tail] 容器名
```

-f --follows = true|false  默认false （一直跟踪最新的日志）

-t -- timestamps = true | false  默认false

--tail = all



#### 查看容器内的进程

```
docker top 容器名
```

```shell
[root@iz2ze20vl8jnph0si0n7jsz ~]# docker top dc1
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                4720                4707                0                   20:09               ?                   00:00:00            /bin/bash -c while true; do echo hello world;sleep 1;done
root                5061                4720                0                   20:13     
```



#### 在运行的容器中启动新的进程

```
docker exec [-d] [-i] [-t] 容器名 [command] [args]
```



#### 停止守护式容器

```
docker stop 容器名 (发送信号，等待停止）
docker kill 容器名（直接停止）
```



### 容器实战

#### 在容器中部署静态网站

##### 设置容器的端口映射

```
run [-P] [-p]
```

-P: --publish-all = true|false 默认是false

比如`docker run -P -i -t ubuntu /bin/bash`



-p: --publish=[]  就是指定映射的端口号

指定端口有几种方式：

方式一：containerPort（只指定容器的端口，宿主机的端口随机）

`docker run -p 80 -i -t ubuntu /bin/bash`

方式二：hostPort：containerPort（宿主机和容器的端口都指定，感觉有点映射的感觉）

`docker run -p 8080：80 -i -t ubuntu /bin/bash`

方式:三：IP：containerPort（指定ip和容器端口）

`docker run -p 0.0.0.0：80 -i -t ubuntu /bin/bash`

方式:四：IP：hostPort：containerPort（）

`docker run -p 0.0.0.0：8080：80 -i -t ubuntu /bin/bash`



##### 安装nginx

我们使用命令

```
apt-get install -y nginx
```

来安装 nginx，但是发现报如下的错误：

```
Reading package lists... Done
Building dependency tree       
Reading state information... Done
E: Unable to locate package vim
```

这个时候可以`apt-get update`操作，等更新完成之后在安装就可以了



##### 安装vim

同理使用如下的命令：

```shell
apt-get install -y vim
```



##### 创建存放网站的目录

```shell
mkdir /var/www/html
cd /var/www/html
vim index.html
```



##### 编辑nginx的配置文件

先查找nginx在哪儿

```shell
whereis nginx
```

```shell
ls /etc/nginx
vim /etc/nginx
```



# 实战遇到的问题

## Cannot connect to the Docker daemon at XXX

错误详情是如下：

```
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```

问题原因是因为docker服务没有启动，所以在相应的`/var/run/` 路径下找不到docker的进程。

执行 `service docker start` 命令，启动docker服务，返回