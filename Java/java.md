# Java

java [-options] class [args...]

java [-options] -jar jarfile [args...]

optioins

-server 选择 "server" VM，VM 是 server.

 -D<名称>=<值> 设置系统属性

eg：

java -Xms64m -Xmx128m  -DprocessType=1  -jar xxx.jar --spring,config.location=xxx --spring.profiles.active=dev --server.port=8080 processType2=2

System.getProperty("processType")



 @VisibleForTesting 将私有的方法改为protected，仅在测试中生效



java -jar -DJAVA_LOCALIP=127.0.0.1 -DJAVA_CONSULACLTOKEN=aad1c625-f959-008b-5621-d68600d4e6a7





jvisualvm的Threads Inspector

检测到死锁！生成一个线程 Dump 以获取更多信息。

根据进程pid生成dump文件

jmap -dump:format=b,file=heap.hprof pid



jhat -port 5000 heap.hprof

http://localhost:5000/

