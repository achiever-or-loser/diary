## [Centos7 虚拟机安装](https://www.cnblogs.com/jpfss/p/10911463.html)

net公用主机；bridge单独的

MB等单位以10为底数的指数,MiB是以2为底数的指数

1KB=10^3=1000

1KiB=2^10=1024

/ /boot /swap

yum install net-tools

yum install wget

yum install lrzsz



[vim 安装使用](./vim.md)



## 修改固定IP

1. 配置ip地址等信息

   编辑/etc/sysconfig/network-scripts/ifcfg-ens33

~~~
BOOTPROTO=static        #开机协议，有dhcp及static；
ONBOOT=yes              #设置为开机启动；
IPADDR=192.168.160.22	#要配置的固定IP，和物理机网段一致，否则有可能用xshell连接失败
GATEWAY=192.168.160.2	#注意，否则可能外网不通
NETMASK=255.255.255.0
DNS1=8.8.8.8			#DNS 8.8.8.8为Google提供的免费DNS服务器的IP地址
~~~

2. 配置网络工作

vim /etc/sysconfig/network添加

```
NETWORKING=yes # 网络是否工作，此处一定不能为no
```

3. 配置公共DNS服务(可选)

在/etc/resolv.conf文件里增加如下配置

```
nameserver 8.8.8.8
```

4. 关闭防火墙

systemctl stop firewalld # 临时关闭防火墙

systemctl disable firewalld # 禁止开机启动

5. 重启网络服务

service network restart

## 修改主机名

https://www.cnblogs.com/zhaojiedi1992/p/zhaojiedi_linux_043_hostname.html

```
hostname # 查看主机名
hostnamectl
cat /etc/hosts  
```

centos6下修改	vim /etc/sysconfig/network   

centos7修改	set-hostname centos77.magedu.com	# 使用这个命令会立即生效且重启也生效

## 修改yum

```
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

https://blog.csdn.net/xiaojin21cen/article/details/84726193

[源配置](https://www.cnblogs.com/handongyu/p/10272190.html)

```
yum update   #升级所有包同时，也升级软件和系统内核；
yum upgrade   #只升级所有包，不升级软件和系统内核，删除旧版本的package
```

以上是网上大多数说法，但是从打印的信息看两个都会去更新内核。



修改`/etc/yum.conf`在[main]配置添加配置项仍然不起作用，可能因为缓存要更新

exclude=kernel*

exclude=centos-release*

在更新时使用命令添加排除项

```ruby
$ yum --exclude=kernel* --exclude=centos-release* -y update
```



--obsoletes 
　　而默认中/etc/yum.conf配置文件obsoletes=1是打开的，因此这两个指令针对系统内核来说是等价的。针对update根据 obsoletes 配置决定是否更新系统内核。yum 的 upgrade 是等价于打开 obsoletes 配置的 yum update。

yum check-update #哪些已安装的包当前有更新



~~~
yum install wget
# ifconfig netstat
yum install net-tools 
~~~







## [JDK 安装](https://www.cnblogs.com/sxdcgaq8080/p/7492426.html)





## GCC



\#include<stdio.h>

int main()
{
printf("\n hello world \n");
return 0;
}



yum install gcc

yum search gcc-c++

yum install gcc-c++.x86_64



[CentOS系统-常用组件安装](https://www.cnblogs.com/mountain2011/p/7263866.html)

[修改path环境变量](https://www.cnblogs.com/hust-chenming/p/4943268.html)



 /proc/sys/net/core/somaxconn

内核参数默认值一般是128（定义了系统中每一个端口最大的监听队列的长度）


