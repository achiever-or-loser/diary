# redis

# 安装

## wget下载包

使用wget下载包->解压->编译、安装。[详情](https://www.cnblogs.com/heqiuyong/p/10463334.html)

前提已经安装gcc

~~~
wget http://download.redis.io/releases/redis-5.0.3.tar.gz
tar -zxvf redis-5.0.3.tar.gz
cd redis-5.0.3
make
# 安装并指定安装目录
make install PREFIX=/usr/local/redis
~~~

启动服务

默认前台启动：/usr/local/redis/bin/redis-server

设置后台启动：修改 redis.conf 文件（建议备份原始的redis.conf），把 daemonize no 改为 daemonize yes

/usr/local/redis/bin/redis-server ./redis.conf 

## yum安装

也可以使用yum安装，这样可以直接使用systemctl以服务形式管理redis。[详情](https://www.cnblogs.com/hjw-zq/p/9778514.html)

~~~
yum install redis
yum install epel-release
systemctl start redis
~~~

## docker安装

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

# 主从

主要配置每个`redis.conf`启动的端口，另外从节点要配置主节点的IP和端口。其他的相关信息根据需要自己配置。redis-5.0的版本是`replicaof`其他版本可能是`slaveof`等有所区别。

```
replicaof IP port
```

使用`redis-cli`连接后可以使用`info replication`查看主从信息

# 哨兵

在原来的集群的基础上配置，主要配置每个`sentinel.conf`哨兵服务的端口和监听的主节点

```
sentinel monitor <master‐name> <ip> <redis‐port> <quorum>
```

quorum：指明哨兵集群中多少个sentinel认为master失效才真的失效，一般配节点数/2+1，也就是说大于半数，比如三个哨兵节点组成的哨兵集群，就配置2，如下：

sentinel monitor mymaster 127.0.0.1 6379 2

哨兵会去==修改新的主节点==的配置文件`redis.conf`，下次再启动主从节点可能会改变。

# 集群

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

# 问题

WARNING: IPv4 forwarding is disabled. Networking will not work.

开启IP路由转发

```
echo net.ipv4.ip_forward=1 >> /usr/lib/sysctl.d/00-system.conf 
systemctl restart network
```



polkitd 服务

容器中连接问题