# Feigin

伪HTTP；http请求调用的轻量级框架

[官网文档](https://cloud.spring.io/spring-cloud-openfeign/reference/html/#spring-cloud-feign)

[设计流程](https://img-blog.csdn.net/20180925130831141?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTAzNDkxNjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

ReflectiveFeign#newInstance解析；Contract 协议；基于RequestBean动态生成Request；用Encoder 将Bean转换成 Http报文正文；拦截器负责对请求和返回进行处理；基于重试器发送HTTP请求；发送Http请求

每次发送请求的时候，都会创建新的HttpURLConnection 链接；可以通过拓展该接口，使用Apache HttpClient 或者OkHttp3等基于连接池的高性能Http客户端

采用池化连接后效率——测试



https://blog.csdn.net/luanlouis/article/details/82821294





![设计流程](https://img-blog.csdn.net/20180925130831141?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTAzNDkxNjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)



对比restTemplate：RestTemplate对象在底层通过ClientHttpRequest.execute()

feign同样可以使用IP的形式发送HTTP请求

