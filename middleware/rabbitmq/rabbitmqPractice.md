# 安装

rabbitmq-server start  -detached

rabbitmqctl stop

docker 安装

```
docker pull rabbitmq：management
docker run -dit --name Myrabbitmq -e RABBITMQ_DEFAULT_USER=admin -e RABBITMQ_DEFAULT_PASS=admin -p 15672:15672 -p 5672:5672 rabbitmq:managemen
```

在容器内需要使用iptables功能

--privileged=true



## Event Exchange Plugin（事件交换器插件）

## 相关单位符号

~~~
## k, kiB: kibibytes (2^10 - 1,024 bytes)
## M, MiB: mebibytes (2^20 - 1,048,576 bytes)
## G, GiB: gibibytes (2^30 - 1,073,741,824 bytes)
## kB: kilobytes (10^3 - 1,000 bytes)
## MB: megabytes (10^6 - 1,000,000 bytes)
## GB: gigabytes (10^9 - 1,000,000,000 bytes)
~~~





## enabled plugin

rabbitmq-plugins enable rabbitmq_shovel rabbitmq_shovel_management

rabbitmq-plugins enable rabbitmq_event_exchange

https://www.rabbitmq.com/event-exchange.html

event test



## 问题

1. User can only log in via localhost。

   在rabbitmq的配置文件目录下（默认为：/etc/rabbitmq）创建一个**rabbitmq.config**文件。

文件中添加如下配置（请不要忘记那个“**.**”）：

[{rabbit, [{loopback_users, []}]}]. 

将：{loopback_users, [<<”guest”>>]}， 改为：{loopback_users, []}，

 systemctl restart rabbitmq-server.service

2. docker镜像创建的容器用户名密码admin

