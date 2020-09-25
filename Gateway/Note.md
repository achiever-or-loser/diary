# 断言中*和**

\* 表示后边只有以及目录

\- Path=/gateway/*

只能匹配/gateway/get；不能匹配/gateway/get/getget。

后边有两个*时可以匹配

# Cors问题

错误信息很好理解，就是说`Access-Control-Allow-Origin`有两个值，但是浏览器只准许有一个值，所以报错。



![image-20200924151336101](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200924151336101.png)

![image-20200924151648045](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200924151648045.png)

我们发现在 `response headers` 中 `Access-Control-Allow-Origin` 出现了两次。由于是在 `response` 中这说明我们是在后端出现的问题。

如果后端属于标准的微服务架构，也就是：h5 ->  gateway -> other Micro service，由于我已经在 api gateway 项目中已经加入了 cors，那么在 api 网关（gateway）后面的微服务不在需要加入 cors 了。由于在后端的微服务项目中也同样加入了cors，这样在返回的response中就加入了两个相同的跨域header，这样浏览器发现多个`Access-Control-Allow-Origin`于是就报错了。

解决方案：

1. 将后端cors跨域配置删除即可，只保留API gateway 项目的cors配置。
2. 将gateway中的cors配置的路径改为`/*`即可

https://segmentfault.com/a/1190000009220751

