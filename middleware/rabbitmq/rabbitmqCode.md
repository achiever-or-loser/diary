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