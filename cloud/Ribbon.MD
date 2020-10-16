# Ribbon

![image-20200903153045555](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200903153045555.png)



ConsulServerList#getServers中ConsulClient去请求consul获取健康的服务列表

ConfigurationBasedServerList#getUpdatedListOfServers通过clientConfig获得ListOfServers

ServerList<T> serverListImpl

DynamicServerListLoadBalancer#updateListOfServers根据上下文具体的ServerList更新serverList

initWithNiwsConfig根据IClientConfig(DefaultClientConfigImpl)配置filter，serverlist，

重写DynamicServerListLoadBalancer#updateListOfServers？？consul中没有service读取配置路由

ConsulServerList#getServers？

拦截？

IClientConfig(DefaultClientConfigImpl)根据clientName获得所有的配置信息





```
NFLoadBalancerClassName`: 配置`ILoadBalancer的实现类
NFLoadBalancerRuleClassName: 配置should implement IRule的实现类
NIWSServerListClassName: 配置should implement ServerList的实现类
```



服务注册中心中的服务名不能带下划线

Invalid host: lb://de_mo

RouteToRequestUrlFilter中包含了正则的检查匹配

```java
if ("lb".equalsIgnoreCase(routeUri.getScheme()) && routeUri.getHost() == null) {
    throw new IllegalStateException("Invalid host: " + routeUri.toString());
}
```

consul在注册服务时会对服务的名字进行处理

ConsulAutoRegistration#registration

normalizeForDns 字符串不能为空,首字符必须为字母,尾字符必须为字母或数字,所有非字母数字的字符统一转换成'-'连接符,同时多个连续连接符转换成一个'-'.





ribbon有7种负载均衡策略可供选择：
RandomRule、RoundRobinRule、RetryRule、BestAvailableRule、AvailabilityFilteringRule、ResponseTimeWeightedRule、ZoneAvoidanceRule

| 策略类                    | 命名               | 描述                                                         |
| ------------------------- | ------------------ | ------------------------------------------------------------ |
| RandomRule                | 随机策略           | 随机选择server                                               |
| RoundRobinRule            | 轮询策略           | 按照顺序选择server（ribbon默认策略）                         |
| RetryRule                 | 重试策略           | 在一个配置时间段内，当选择server不成功，则一直尝试选择一个可用的server |
| BestAvailableRule         | 最低并发策略       | 逐个考察server，如果server断路器打开，则忽略，再选择其中并发链接最低的server |
| AvailabilityFilteringRule | 可用过滤策略       | 过滤掉一直失败并被标记为circuit tripped的server，过滤掉那些高并发链接的server（active connections超过配置的阈值） |
| ResponseTimeWeightedRule  | 响应时间加权重策略 | 根据server的响应时间分配权重，响应时间越长，权重越低，被选择到的概率也就越低。响应时间越短，权重越高，被选中的概率越高，这个策略很贴切，综合了各种因素，比如：网络，磁盘，io等，都直接影响响应时间 |
| ZoneAvoidanceRule         | 区域权重策略       | 综合判断server所在区域的性能，和server的可用性，轮询选择server并且判断一个AWS Zone的运行性能是否可用，剔除不可用的Zone中的所有server |







consul 定时刷新时间https://www.cnblogs.com/kevin-yuan/p/13362057.html