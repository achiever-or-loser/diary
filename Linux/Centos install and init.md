## [Centos7 虚拟机安装](https://www.cnblogs.com/jpfss/p/10911463.html)

net公用主机；bridge单独的

MB等单位以10为底数的指数,MiB是以2为底数的指数

1KB=10^3=1000

1KiB=2^10=1024

/ /boot /swap

yum install net-tools

yum install wget

yum install lrzsz

## vim

vim编辑器需要安装三个包：vim-enhanced-版本、vim-minimal-版本、vim-common-版本

```
rpm -qa|grep vim # 查看已经安装的包，确认VIM是否已经安装
yum -y install vim-enhanced # 安装缺少的某个
yum -y install vim* # 安装所有vim需要的
```



1,10d # 删除1-10行

ndd # 从当前行删除n行

dd

yy p

6,9 co 12 # 复制第6行到第9行之间的内容到第12行后面

u<---->Ctrl+R

可视模式是选中一块区域进行操作，包括删除，替换，复制，粘贴，改变大小写等。在正常模式下按v（小写）进入字符文本，按V（大写）进入行文本，按ctrl+v进入块文本。然后使用上下左右键进行操作选中区域。对选中的部分使用d进行删除，y进行复制，p进行粘贴，r进行文本替换，gu转换为小写，gU转换为大写，g~大小写互换。

## 修改固定IP

1. 配置ip地址等信息

   编辑/etc/sysconfig/network-scripts/ifcfg-ens33

~~~
BOOTPROTO=static        #开机协议，有dhcp及static；
ONBOOT=yes              #设置为开机启动；
IPADDR=192.168.160.22	#要配置的固定IP，和物理机网段一致，否则有可能用xshell连接失败
GATEWAY=192.168.160.2	#注意，否则可能外网不通
NETMASK=255.255.255.0
DNS1=8.8.8.8			#DNS 8.8.8.8为Google提供的免费DNS服务器的IP地址
~~~

2. 配置网络工作

vim /etc/sysconfig/network添加

```
NETWORKING=yes # 网络是否工作，此处一定不能为no
```

3. 配置公共DNS服务(可选)

在/etc/resolv.conf文件里增加如下配置

```
nameserver 8.8.8.8
```

4. 关闭防火墙

systemctl stop firewalld # 临时关闭防火墙

systemctl disable firewalld # 禁止开机启动

5. 重启网络服务

service network restart

## 修改主机名

https://www.cnblogs.com/zhaojiedi1992/p/zhaojiedi_linux_043_hostname.html

```
hostname # 查看主机名
hostnamectl
cat /etc/hosts  
```

centos6下修改	vim /etc/sysconfig/network   

centos7修改	set-hostname centos77.magedu.com	# 使用这个命令会立即生效且重启也生效

## 修改yum

```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

https://blog.csdn.net/xiaojin21cen/article/details/84726193

[源配置](https://www.cnblogs.com/handongyu/p/10272190.html)

```
yum update   #升级所有包同时，也升级软件和系统内核；
yum upgrade   #只升级所有包，不升级软件和系统内核，删除旧版本的package
```

以上是网上大多数说法，但是从打印的信息看两个都会去更新内核。



修改`/etc/yum.conf`在[main]配置添加配置项仍然不起作用，可能因为缓存要更新

exclude=kernel*

exclude=centos-release*

在更新时使用命令添加排除项

```ruby
$ yum --exclude=kernel* --exclude=centos-release* -y update
```



--obsoletes 
　　而默认中/etc/yum.conf配置文件obsoletes=1是打开的，因此这两个指令针对系统内核来说是等价的。针对update根据 obsoletes 配置决定是否更新系统内核。yum 的 upgrade 是等价于打开 obsoletes 配置的 yum update。

yum check-update #哪些已安装的包当前有更新



~~~
yum install wget
# ifconfig netstat
yum install net-tools 
~~~







## [JDK 安装](https://www.cnblogs.com/sxdcgaq8080/p/7492426.html)



## Docker

[docker 安装](https://www.cnblogs.com/lylsr/p/11173003.html)

yum install yum-utils device-mapper-persistent-data lvm2

卸载以前安装的

查看版本

安装



容器

~~~
systemctl start docker
docker version #有client和service两部分表示docker安装启动都成功
docker run -itd	#i交互式；t终端；d后台运行
docker ps 
docker start id
docker stop id
docker restart id
docker rm -f id

docker attach #退出容器终端容器不会停止
docker exec	#退出容器终端容器停止

docker build name:tag ./Dockerfile

docker export id > xxx.tar
docker import URL
cat xxx.tar | docker import - xxx:v1

~~~

docker rm ： 删除一个或多个 容器

docker rmi： 删除一个或多个 镜像

镜像

~~~
docker images
docker pull imageName
docker search imageName
docker rmi imageName
docker commit
docker build
docker tag
~~~

到处到本地

docker commit -m 'message' -a ='author' <containerId> repository:tag

获取容器/镜像的元数据。

```
docker inspect 容器id 
```

[Docker 网络](https://www.jianshu.com/p/d84cdfe2ea86)

### Docker-Compose

[官方文档](https://docs.docker.com/compose/install/)

Docker Compose 是用于定义和运行多容器 Docker 应用程序的工具。可以使用 YML 文件配置应用程序需要的所有服务,然后使用命令从 YML 文件配置中创建并启动所有服务。


对于`alpine`，需要以下依赖包：`py-pip`、`python-dev`、`libffi-dev`、`openssl-dev`、`gcc`、`lib-dev`、`make`

下载Docker-Compose文件

`sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`

> PS:如果下载速度慢，可尝试换别的源下载

```
sudo curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose`
```

给文件添加执行权限

`sudo chmod +x /usr/local/bin/docker-compose`

验证

`docker-compose --version`

可能需要手动添加软连接

`sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose`

### registry

[registry 官方](https://docs.docker.com/registry/deploying/)



## GCC







\#include<stdio.h>

int main()
{
printf("\nLinux公社(www.linuxidc.com)是专业的Linux系统门户网站\n\n");
return 0;
}

## nginx

https://www.cnblogs.com/future-dream/p/12375258.html

yum install gcc

yum install  pcre pcre-devel

yum install zlib zlib-devel

yum install openssl openssl-devel

/etc/nginx



yum search gcc-c++

yum install gcc-c++.x86_64

## redis

### 安装

使用wget下载包->解压->编译、安装。[详情](https://www.cnblogs.com/heqiuyong/p/10463334.html)

也可以使用yum安装，这样可以直接使用systemctl以服务形式管理redis。[详情](https://www.cnblogs.com/hjw-zq/p/9778514.html)

使用docker

~~~
docker pull redis
docker run -p 6379:6379 --name redis-dev -v /home/csc/download/redis-5.0.3/redis.conf:/etc/redis/redis.conf -v /home/csc/download/redis-5.0.3/data/:/data -itd redis --appendonly yes
docker run -p 6379:6379 --name redis-dev -v /home/csc/download/redis-5.0.3/data/:/data -itd redis
~~~

`redis.conf`中常用的配置，每个配置在模板文件里都有详细的说明

```
# 默认情况下 redis 不是作为守护进程运行的，如果需要在后台运行，就把改成 yes。
# 当redis作为守护进程运行的时候，它会写一个 pid 到 /var/run/redis.pid 文件里面。
daemonize no

# 设置数据库的数目。
databases 16

# redis在server上所有有效的网络接口上监听客户端连接。多个IP时用空格隔开
# bind 192.168.1.100 10.0.0.1
bind 127.0.0.1

# 监听端口号，默认为 6379，如果你设为 0 ，redis 将不在 socket 上监听任何客户端连接。
port 6379

# 使用 slaveof 来让一个 redis 实例成为另一个reids 实例的副本。
# 注意这个只需要在 slave 上配置。有的版本是 replicaof
# slaveof <masterip> <masterport>
配置一个 slave 实体是否接受写入操作。
slave-read-only yes
```

使用`redis-cli`连接到redis-server，语法

```
redis-cli -h host -p port -a password
```

### 主从

主要配置每个`redis.conf`启动的端口，另外从节点要配置主节点的IP和端口。其他的相关信息根据需要自己配置。redis-5.0的版本是`replicaof`其他版本可能是`slaveof`等有所区别。

```
replicaof IP port
```

使用`redis-cli`连接后可以使用`info replication`查看主从信息

### 哨兵

在原来的集群的基础上配置，主要配置每个`sentinel.conf`哨兵服务的端口和监听的主节点

```
sentinel monitor <master‐name> <ip> <redis‐port> <quorum>
```

quorum：指明哨兵集群中多少个sentinel认为master失效才真的失效，一般配节点数/2+1，也就是说大于半数，比如三个哨兵节点组成的哨兵集群，就配置2，如下：

sentinel monitor mymaster 127.0.0.1 6379 2

哨兵会去==修改新的主节点==的配置文件`redis.conf`，下次再启动主从节点可能会改变。

### 集群

Redis-Cluster采用无中心结构，每个节点保存数据和整个集群状态,每个节点都和其他所有节点连接。

修改redis.conf，

```bash
cluster-enabled yes #开启cluster
cluster-config-file nodes.conf #自动生成
cluster-node-timeout 15000 #节点通信时间
```

安装环境

~~~
yum install ruby rubygems
gem install redis-3.x.x.gem
~~~

在7000 7001 7002 7003 7004 7005端口启动6个服务。

使用redis-trib.rb的create命令创建集群，redis-trib.rb在redis/src目录下

```undefined
./redis-trib.rb create --replicas 1 127.0.0.1:7001 127.0.0.1:7002 127.0.0.1:7003 127.0.0.1:7004 127.0.0.1:7005 127.0.0.1:7000
```

[除了create的其他命令](https://baijiahao.baidu.com/s?id=1608961712462851811)

### 问题

WARNING: IPv4 forwarding is disabled. Networking will not work.

开启IP路由转发

```
echo net.ipv4.ip_forward=1 >> /usr/lib/sysctl.d/00-system.conf 
systemctl restart network
```



polkitd 服务

容器中连接问题

## RabbitMQ

rabbitmq-server start  -detached

rabbitmqctl stop

docker 安装

```
docker pull rabbitmq：management
docker run -dit --name Myrabbitmq -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 rabbitmq:managemen
```

在容器内需要使用iptables功能

--privileged=true



### 问题

1. User can only log in via localhost。

   在rabbitmq的配置文件目录下（默认为：/etc/rabbitmq）创建一个**rabbitmq.config**文件。

文件中添加如下配置（请不要忘记那个“**.**”）：

[{rabbit, [{loopback_users, []}]}]. 

将：{loopback_users, [<<”guest”>>]}， 改为：{loopback_users, []}，

 systemctl restart rabbitmq-server.service

2. docker镜像创建的容器用户名密码admin



[CentOS系统-常用组件安装](https://www.cnblogs.com/mountain2011/p/7263866.html)

[修改path](https://www.cnblogs.com/hust-chenming/p/4943268.html)



## [curl命令](https://www.cnblogs.com/hujiapeng/p/8470099.html)

保存访问的网页

测试返回值

代理服务器和端口

cookie、response中header

模仿浏览器

下载（循环、分块、ftp）、上传文件



 /proc/sys/net/core/somaxconn

内核参数默认值一般是128（定义了系统中每一个端口最大的监听队列的长度）


