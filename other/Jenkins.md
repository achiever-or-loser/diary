# jenkins

[install](https://www.cnblogs.com/loveyouyou616/p/8714544.html)

安装需要Java环境；配置文件/etc/sysconfig/jenkins；默认在8080端口

~~~
# yum安装
wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
yum install -y jenkins
# rmp 安装
wget http://pkg.jenkins-ci.org/redhat-stable/jenkins-2.7.3-1.1.noarch.rpm
rpm -ivh jenkins-2.7.3-1.1.noarch.rpm

service jenkins start/stop/restart

# yum卸载
yum -y remove jenkins

# rpm卸载
rpm -e jenkins
# 检查是否卸载成功
rpm -ql jenkins 
# 彻底删除残留文件：
find / -iname jenkins | xargs -n 1000 rm -rf
~~~

安装后在8080端口访问，初始化配置；安装插件；

从Git上拉取代码，编译、部署、启动：https://blog.csdn.net/miss1181248983/article/details/82840006



[常见 error](