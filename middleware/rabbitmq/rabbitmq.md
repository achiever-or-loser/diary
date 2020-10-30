# rabbitmq

## 基础

connectionFactory 

connection 是rabbitmq的socket连接，封装了socket协议相关

channel 包括定义exchange、binding、queue

exchange 交换器，将消息路由到一个或多个queue中

binding 将exchange和queue关联起来

binding key 在绑定（Binding）Exchange与Queue的同时，一般会指定一个bingding key;消费者将消息发送给Exchange时，一般会指定一个routing key;当binding key与routing key相匹配时，消息将会被路由到对应的Queue中。

## exchange type

Exchange Types 常用的有fanout、direct、topic、headers这四种

- fanout

fanout类型的Exchange路由规则非常简单，它会把所有发送到该Exchange的消息路由到所有的与它绑定的Queue中。与routingKey没有关系

- direct

direct类型的Exchange路由规则也很简单，它会把消息路由到哪些binding key与routing key完全匹配的Queue中。

- topic

topic类型的Exchange在匹配规则上进行了扩展，它与direct类型的Exchange相似，也是将消息路由到binding key与routing key相匹配的Queue中，但是匹配规则不同：

routing key是一个句点号"."分割的字符串（我们讲被句点号“.”分割开的每一段独立的字符串成为一个单词），如：stock.user.rabbit；

binding key与routing key一样也是句点号“.”分割的字符串；

binding key中可以存在两种特殊的字符“*”与“#”，用于做模糊匹配，其中“\*”用于匹配一个单词，“#”用于匹配多个单词（可以是零个）

- headers

headers类型的Exchange不依赖于routing key与binding key的匹配规则来路由消息，而是根据发送的消息内容中的headers属性进行匹配 在绑定Queue与Exchange时指定一组键值对；当消息发送到Exchange时，RabbitMQ会取到该消息的headers（也是一个键值对的形式），对比其中的键值对是否完全匹配Queue与Exchange绑定时指定的键值对；如果完全匹配则消息会路由到该Queue，否则不会路由到该Queue。 



message acknowledgment 消费者消费完发送一个回执给rabbitmq，rabbitmq收到后将消息从队列中移除；没有收到的话把消息发送到其他消费者

message Durability 把message和queue持久化

prefetch count 流控，同一时间不能发送多于 prefetch count 消费者的消息数







init connectionFactory -> create connection -> create channel -> exchange -> queue ->  binding -> publish

init connectionFactory -> create connection -> create channel -> queue ->  binding -> consume



消费者回调接口consumer	？？？



通过channel删除消费者订阅的队列

channel.queueDelete(QUEUE_NAME);

AMQP.Queue.DeleteOk ok = channel.queueDelete(QUEUE_NAME, ifUnused, ifEmpty);

channel.queuePurge(QUEUE_NAME);



## 队列设置

队列容量设置 x-max-length、x-max-length-bytes、x-overflow

过期时间 x-message-ttl；消息在队列中的生存时间一旦超过设置的TTL值时，就会变成“死信”

```
/**
 * queue溢出行为，这将决定当队列达到设置的最大长度或者最大的存储空间时发送到消息队列的消息的处理方式；
 * 有效的值是：
 * drop-head（删除queue头部的消息）、
 * reject-publish（最近发来的消息将被丢弃）、
 * reject-publish-dlx（拒绝发送消息到死信交换器）
 * 类型为quorum 的queue只支持drop-head;
 */
arg.put("x-over-flow", "reject-publish");
arg.put("x-queue-mode", "lazy");
arg.put("x-message-ttl", 5000);
```



## temp

惰性队列 将尽可能多的消息存入磁盘，消费者消费时消息加载到内存中；场景：消息堆积；减少内存的占用，增加I/O的使用；



镜像队列 队列跨多个节点镜像；一个主服务器和一个或多个镜像组成

流控 一个连接触发流控时会处于flow的状态，也就意味着这个Connection的状态每秒在blocked和unblocked之间来回切换数次，这样可以将消息发送的速率控制在服务器能够支撑的范围之内。流控制不只是作用于Connection，同样作用域Channel和queue，从Connection到Channel,再到queue，最后是消息持久化存储形成一个完整的流控链，对于处于流控链中的任意进程，只要该进程阻塞，上游的进程必定全部阻塞。也就是说，如果某个进程达到性能瓶颈，必然导致上游所有的进程被阻塞；流控制机制是基于信用证（Credit）的拥塞控制机制实现的；

内存警告 默认rabbitmq使用内存超过40%时发出内存警告，阻塞发布消息的连接（发布者会被阻塞），警告解除时（服务器paging消息到磁盘，或者分发消息到消费者并确认），服务恢复正常

磁盘警告 磁盘可用空间下降到配置值（最低默认配置值48MIB）之下触发，生产者阻塞

CTL工具修改配置时，服务重启后设置失效

Prometheus plugin rabbitmq性能指标收集

集群 Exchange元数据信息在所有节点上，Queue值存在创建的那个节点上，其他节点值知道这个queue的metadata信息和一个指向queue的owner node的指针；交换机、绑定、队列、vhost元数据；磁盘节点和RAM节点：RAM节点仅将内部数据表存储在RAM中，不包括消息、消息存储索引、队列索引和其他节点状态，提高绑定交换频率较高的性能，启动时从对等节点同步他们，一个集群至少包含一个磁盘节点。从节点使用--link到主，Erlang Cookie值必须相同，--ram 表示内存节点；重置节点将删除其所有数据，集群成员信息，已配置的运行时参数，用户，虚拟主机以及任何其它节点数据，它还将从该群集中永久删除该节点。必须先停止在重置或重启	

死信交换机DLX 消费者拒绝；消息过期；队列长度限制被丢弃；修改死信消息头部信息；

TCP buffer size 吞吐量，每个连接能使用的RAM的大小；

消费者优先级 高优先级消费者先消费

消费者预取消息 basicQos

消息发布确认 

消息服务进程

~~~
# 停止erlang虚拟机和RabbitMQ应用服务器
rabbitmqctl stop
# 停止rabbitmq应用，不停止Erlang VM
rabbitmqctl stop_app
~~~

消息优先级 x-max-priority ; properties.priority

消息追踪 Firehose amq.rabbitmq.trace一个topic类型的交换器，路由键publish.{exchangename}，deliver.{queuename}，记录每条消息，性能降低

rabbitmq_tracing插件 Firehose 的GUI

虚拟主机 Virtual vhost 本质是一个mini版的Rabbitmq服务器，拥有自己的connection、exchange等，vhost于rabbitmq就像虚拟机于物理机，逻辑上的分离，

延迟队列 存储延迟消息(等待特定时间后消费者拿到消息进行消费)，场景：下单后有一段时间继续支付

basicConsume



spring_listener_return_correlation

spring_returned_message_correlation







工作队列

默认将每个消息按顺序发送给下一个使用者，

消息确认 未确认的消息将会重新发送；丢失确认，耗内存，rabbitmqctl打印

消息持久化 不能完全保证不会丢失消息，在缓存中没有写入磁盘

公平分配 basicQos在处理并确认上一条消息之前，不要将新消息发送给消费者。而是将其分派给不忙的下一个工作程序。





## BasicPropertie

1. contentType:消息的内容类型，如：text/plain
2. contentEncoding:消息内容编码
3. headers:设置消息的header,类型为Map<String,Object>
4. deliveryMode:1（nopersistent）非持久化，2（persistent）持久化
5. priority:消息的优先级
6. correlationId:关联ID
7. replyTo:用于指定回复的队列的名称
8. expiration:消息的失效时间
9. messageId:消息ID
10. timestamp:消息的时间戳
11. type:类型
12. userId:用户ID
13. appId：应用程序ID
14. custerId:集群ID

## addReturnListener监听回退消息

~~~java
/**
* exchange:要将消息发送到的Exchange(交换器)
* routingKey:路由Key
* mandatory:true 如果mandatory标记被设置，消息不能路由到指定的队列时，则会调用basic.return方法将消息返回给生产者,会触发addReturnListener注册的监听器；如果为false，则broker会直接将消息丢弃
* immediate: true 如果immediate标记被设置，注意：RabbitMQ服务端不支持此标记
* props:其它的一些属性，如：{@link MessageProperties.PERSISTENT_TEXT_PLAIN}
* body:消息内容
**/
void basicPublish(String exchange, String routingKey, boolean mandatory, boolean immediate, BasicProperties props, byte[] body)

# 监听回退消息
channel.addReturnListener((returnMessage)-> {
	System.out.println("监听器执行了---");
});



~~~

触发情况

setReturnCallback 

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



镜像队列

TTL

DLX

流控状态



demo



Connection和Channel都实现java.io.Closeable。这样，我们无需在代码中显式关闭它们



[官网configure](https://www.rabbitmq.com/configure.html)

[example rabbitmq.conf file](https://github.com/rabbitmq/rabbitmq-server/blob/v3.7.x/docs/rabbitmq.conf.example)





## Todo

消息确认--确认发送到exchange、queue、；

失败重发一定次数 RetryTemplate

回退消息 发送失败；消费失败



重复发送，重复消费

事务

setReturnCallback 执行情况：exchange未能将消息发送到queue

