 Spring Cloud Ribbon是一个基于HTTP和TCP的客户端负载均衡工具，它基于Netflix Ribbon实现。通过Spring Cloud的封装，可以将面向服务的REST模版请求自动转换成客户端负载均衡的服务调用。

微服务架构中使用客户端负载均衡调用非常简单，只需要如下两步：

​    ▪️服务提供者只需要启动多个服务实例并注册到一个注册中心或是多个相关联的服务注册中心。

​    ▪️服务消费者直接通过调用被@LoadBalanced注解修饰过的RestTemplate来实现面向服务的接口调用。

​    这样，就可以将服务提供者的高可用以及服务消费者的负载均衡调用一起实现了

Ribbon的负载均衡，主要通过LoadBalancerClient来实现的，而LoadBalancerClient具体交给了ILoadBalancer来处理，ILoadBalancer通过配置IRule、IPing等信息，并向注册中心获取注册列表的信息，并每隔一定时间一次向注册中心发送“ping”,进而检查是否更新服务列表，最后，得到注册列表后，ILoadBalancer根据IRule的策略进行负载均衡。

而RestTemplate 被@LoadBalance注解后，能过用负载均衡，主要是维护了一个被@LoadBalance注解的RestTemplate列表，并给列表中的RestTemplate添加拦截器，进而交给负载均衡器去处理。

# 负载均衡策略

ribbon有7种负载均衡策略可供选择：

| 策略类                    | 命名               | 描述                                                         |
| ------------------------- | ------------------ | ------------------------------------------------------------ |
| RandomRule                | 随机策略           | 随机选择server                                               |
| RoundRobinRule            | 轮询策略           | 按照顺序选择server（ribbon默认策略）                         |
| RetryRule                 | 重试策略           | 在一个配置时间段内，当选择server不成功，则一直尝试选择一个可用的server |
| BestAvailableRule         | 最低并发策略       | 逐个考察server，如果server断路器打开，则忽略，再选择其中并发链接最低的server |
| AvailabilityFilteringRule | 可用过滤策略       | 过滤掉一直失败并被标记为circuit tripped的server，过滤掉那些高并发链接的server（active connections超过配置的阈值） |
| ResponseTimeWeightedRule  | 响应时间加权重策略 | 根据server的响应时间分配权重，响应时间越长，权重越低，被选择到的概率也就越低。响应时间越短，权重越高，被选中的概率越高，这个策略很贴切，综合了各种因素，比如：网络，磁盘，io等，都直接影响响应时间 |
| ZoneAvoidanceRule         | 区域权重策略       | 综合判断server所在区域的性能，和server的可用性，轮询选择server并且判断一个AWS Zone的运行性能是否可用，剔除不可用的Zone中的所有server |

# 与consul注册中心



![image-20200903153045555](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200903153045555.png)

ConsulServerList#getServers中ConsulClient去请求consul获取健康的服务列表，这个类包含了对ConsulClient和ConsulDiscoveryProperties的维护

ConfigurationBasedServerList#getUpdatedListOfServers通过clientConfig获得ListOfServers；这是对自定义的服务列表的处理

![image-20201016162401539](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20201016162401539.png)

ILoadBalancer

addServers()方法是添加一个Server集合；chooseServer()方法是根据key去获取Server；markServerDown()方法用来标记某个服务下线；getReachableServers()获取可用的Server集合；getAllServers()获取所有的Server集合。

DynamicServerListLoadBalancer#initWithNiwsConfig根据配置初始化

#updateListOfServers根据上下文具体的ServerList更新serverList，使用consul等服务注册中心的话会定时刷新服务列表

getInitialListOfServers()和getUpdatedListOfServers()



com.netflix.loadbalancer.PollingServerListUpdater#start()  // 定时任务，30s 更新一次最新的服务列表信息
    --> org.springframework.cloud.consul.discovery.ConsulServerList#getUpdatedListOfServers() // 断点，每隔段时间就会被调用



# 与Gateway网关

结合gateway网关实现路由负载均衡

## 自定义服务列表

```
myGateway:
  ribbon:
    listOfServers: localhost:8080, localhost:8081
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RoundRobinRule

databaseOfTrade:
  ribbon:
    listOfServers: http://10.205.9.132:8112, http://10.205.9.133:8112, http://10.205.9.134:8112
    NFLoadBalancerRuleClassName: com.netflix.loadbalancer.RoundRobinRule
```

~~~
NFLoadBalancerClassName: 配置ILoadBalancer的实现类，路由的处理
NFLoadBalancerRuleClassName: 配置IRule的实现类，负载均衡策略
NIWSServerListClassName: 配置ServerList的实现类，列表的存储处理（ConfigurationBasedServerList/ConsulServerList）
NFLoadBalancerPingClassName: 配置IPing的实现类
NIWSServerListFilterClassName: 配置ServerListFilter的实现类
~~~

## 使用consul注册中心

使用注册中心时只需要在需要路由的url值改为服务名即可

~~~
spring:
  cloud:
    gateway:
      routes:
        - id: databaseOfTrade
          uri: lb://databaseOfTrade
          predicates:
           - Path=/databaseOfTrade/**
          filters:
            - StripPrefix=1
~~~

## 同时使用两者

同时使用注册中心和自定义列表时可以对网关加全局过滤器实现可以参考DynamicServerListLoadBalancer（注册中心列表）和ConfigurationBasedServerList（自定义列表）的处理

1. 在consul注册中心基础上找不到响应的服务时加载自定义的服务列表
2. 在自定义列表的基础上路由之前使用consulClient查找consul中心的服务列表路由



























