# Maven

下载自己的Maven后主要配置LocalRepository、mirrors，私有仓库的话要配置server；也可以自己配置一下profile中jdk等相关信息。[Maven setting 文件详解](https://www.cnblogs.com/hongmoshui/p/10762272.html)

附一个阿里镜像

~~~xml
<mirror>
    <id>alimaven</id>
    <mirrorOf>central</mirrorOf>
    <name>aliyun maven</name>
  <url>http://maven.aliyun.com/nexus/content/repositories/central/</url>
</mirror>
~~~

常用命令：

package时自动执行clean+compile

打包时带参数

mvn package -DJAVA_LOCALIP=127.0.0.1 -DJAVA_CONSULACLTOKEN=6c42cbef-cfcd-3d31-eee3-4db94563691d

~~~
mvn clean
mvn compile
mvn install
mvn package
mvn dependency:list
mvn deploy
mvn test
mvn test-compile
mvn -Dtest package
mvn jar:jar
mvn source:jar
~~~



# scope

~~~
provide 在build和test阶段需要，在runtime阶段不需要，由容器提供。eg:web war包不包括servlet-api.jar，而是由tomcat提供
runtime 在编译阶段不需要，在test和runtime需要，代码需要根据配置在运行时动态加载并实例化；好处：壁板程序意外地直接引用原本应该动态加载的包。eg：JDBC连接池，commons-dbcp
compile 默认值，在build、test、runtime阶段都有依赖
test 旨在test阶段有依赖。eg:junit
~~~

1.scope为import只能在dependencyManagement中使用，且type为pom类型。

2.dependencyManagement里配置并不会实际引入，只是为了版本管理，实际引入需要直接在dependencies中添加。

scope import 可以导入其它pom中的依赖

```
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-dependencies</artifactId>
            <version>${spring-cloud.version}</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
```

