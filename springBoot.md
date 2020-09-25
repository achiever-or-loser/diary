# 启动

spring.factories 获取工厂信息；初始化；AppClassLoader；ConfigurableApplicationContext

## 初始化模块


new SpringApplication(primarySources)

基本的环境变量、资源、构造器、监听器


webApplicationType；ApplicationContextInitializer；ApplicationListener



## 具体启动方案


prepareContext


监听模块、加载配置环境模块、及核心的创建上下文环境模块

## 自动化配置









# active 和 include

springBoot的application配置文件中`active`和`include`

启动命令spring.profiles.active指定文件中的值 > 文件中spring.profiles.active指定的文件列表中最后一次出现的值 > 文件中spring.profiles.include指定的文件列表中最后一次出现的值 

```
spring.profiles.include=dev2,dev1
spring.profiles.active=dev,prod
# The following profiles are active: dev2,dev1,dev,prod
```

~~~
java -jar demo1-0.0.1-SNAPSHOT.jar --spring.profiles.active=dev --server.port=8075
# 以命令行中指定的文件为准
~~~

# 获取路径

class.getResourceAsStream(path)	相对路径；path 不以’/‘开头时默认是从此类所在的包下取资源，以’/'开头则是从ClassPath(Src根目录)根下获取。只是通过path构造一个绝对路径，最终还是由ClassLoader获取资源。

class.getClassLoader().getResourceAsStream(path)	绝对路径；从classPath下获取，path不以‘/’开头

# 配置文件优先级

```
1.1 springboot启动会扫描一下位置的application.properties或者application.yml作为默认的配置文件
工程根目录:./config/
工程根目录：./
classpath:/config/
classpath:/
加载的优先级顺序是从上向下加载，并且所有的文件都会被加载，高优先级的内容会覆盖底优先级的内容，形成互补配置

也可以通过指定配置spring.config.location来改变默认配置，一般在项目已经打包后，我们可以通过指令 　　java -jar xxxx.jar --spring.config.location=D:/kawa/application.yml来加载外部的配置
```

# 依赖问题

查询spring-boot-starter-web-2.3.0.RELEASE.pom没有validation对应的包，所以要想使用校验功能要手动引入包：

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```



