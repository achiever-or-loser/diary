



## Gateway

[官方文档](https://cloud.spring.io/spring-cloud-gateway/reference/html/)

[中文文档](https://cloud.tencent.com/developer/article/1403887)

[实例](https://www.cnblogs.com/crazymakercircle/p/11704077.html)

基于==WebFlux==框架实现底层使用了高性能的==Reactor==模式通信框架==Netty==。

（1）基于 Spring Framework 5，Project Reactor 和 Spring Boot 2.0

（2）集成 Hystrix 断路器

（3）集成 Spring Cloud DiscoveryClient

（4）Predicates 和 Filters 作用于特定路由，易于编写的 Predicates 和 Filters

（5）具备一些网关的高级功能：动态路由、限流、路径重写

**（**1**）**Filter**（过滤器）**：

和Zuul的过滤器在概念上类似，可以使用它拦截和修改请求，并且对上游的响应，进行二次处理。过滤器为org.springframework.cloud.gateway.filter.GatewayFilter类的实例。

（2）**Route**（路由）：

网关配置的基本组成模块，和Zuul的路由配置模块类似。一个**Route模块**由一个 ID，一个目标 URI，一组断言和一组过滤器定义。如果断言为真，则路由匹配，目标URI会被访问。

**（**3**）**Predicate**（断言）**：

这是一个 Java 8 的 Predicate，可以使用它来匹配来自 HTTP 请求的任何内容，例如 headers 或参数。**断言的**输入类型是一个 ServerWebExchange。



通过HandlerMapping对请求的链接进行解析，匹配对应的Route进行代理转发到对应的服务。

1.请求先由DispatcherHanlder进行处理，DispatcherHanlder初始化的时候会从IOC中查找实现HandlerMapping接口的实现类。然后保存到内部变量handlerMappings中，DispatcerHandler调用Handler方法迭代handlerMappings中的HandlerMapping,
 2.这里只讲解==RoutePredicateHandlerMapping==，因此然后调用RoutePredicateHandlerMapping中的获取路由的方法，当RoutePredicateHandlerMapping获取到对应的路由的时候会将Route存储到ServerWebExchanges的属性中，然后返回实现了WebHandler接口的==FilteringWebHandler==。FilteringWebHandler是一个存放过滤器的Handler。
 3.最后DispatcherHanlder通过SimpleHandlerAdapter适配器的方式调用FilteringWebHandler的handler方法，FilteringWebHandler调用所有的过滤器，包括proxy filter。通过==proxyFilter==请求被代理的服务。处理完毕后，并将Response响应回去。

适配器模式



![Spring Cloud Gateway Diagram](https://cloud.spring.io/spring-cloud-gateway/reference/html/images/spring_cloud_gateway_diagram.png)

AbstractHandlerMapping

HandlerMapping和Ordered接口主要定义了获取getHandler和当前hanler加载顺序。getHanlder封装了CORS处理，getHandlerInternal让子类实现具体的查找Handler的方法。

父类定义共性，子类实现特有。模板模式



RoutePredicateHandlerMapping

处理获取路由的hanlder。RouteLocator是存储了启动时加载的路由对象信息。getHandlerInternal方法从RouteLocator获取路由存放在ServerWebExchange中，返回webFilter。

在GatewayAutoConfiguration进行自动创建，创建的时候指定webHandler和routeLocator。webHandler就是一个封装了Global Filter的对象，而routeLocator就是保存了所有Route的对象。

Route主要为三部分：

- Proxy：代理的信息包括被代理的uri。
- Predicate:包含接受请求的方法、path等信息
- Filter:Route自定义的过滤器。

RouteLocator加载过程

![RouteLocator加载过程.jpg](https:////upload-images.jianshu.io/upload_images/3047136-43e944476873dc85.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1108/format/webp)

最总的 RouteLocator是CachingRoutelocator。加载过程是自上而下进行创建。Route加载来源分为三种来源:

第一种是通过RouteLocatorBuilder方式

第二种方式是通过Properties文件进行创建路由。Properties路由的创建包括：PropertiesRouteDefinitionLocator(代码创建)和DiscoveryClientRouteDefinitionLocator(服务名称).

第三种方式是通过MYSQL或者Reids、内存（InMemoryRouteDefinitionRepository）方式创建路由。实现RouteDefinitionRepository接口实现接口中的方式。

Filter分为Globle Filter和RouteFilter

- Globle Filter是是在GatewayAutoConfiguration中通过@Bean的方式进行创建。

  ![image-20200806161026588](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200806161026588.png)

- Route Filter是在创建的Route的时候指定。这种路由属于自定义的路由。

最终的代理请求是通过一个Proxy Filter进行请求Proxy Service，那么这个Proxy Filter就是NettyRoutingFilter

```Java
((RequestSender)this.httpClient.chunkedTransfer(chunkedTransfer).request(method).uri(url)).send((req, nettyOutbound) -> {
```

链接：https://www.jianshu.com/p/af1554553b5c


[网关初始化 AutoConfiguration](https://blog.csdn.net/github_38592071/article/details/79765968)



熔断降级

限流



## 问题

服务熔断降级中配置正确，不生效

// 在服务熔断中如果在配置文件中和config中都配饰相同的路由，熔断失效



[限流失效 no configuration found for route](https://blog.csdn.net/zl1zl2zl3/article/details/83382800)











pom 版本

springboot版本与springcloud对应支持版本

~~~
<version>2.1.8.RELEASE</version>
<spring-cloud.version>Greenwich.SR3</spring-cloud.version>
~~~

```
<version>2.2.1.RELEASE</version>
<spring-cloud.version>Hoxton.RELEASE</spring-cloud.version>
```

