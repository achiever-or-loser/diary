# nginx

https://www.cnblogs.com/future-dream/p/12375258.html

## 源码安装



源码安装编译依赖gcc


~~~
yum install gcc

wget http://nginx.org/download/nginx-1.16.1.tar.gz
tar -xzf nginx-1.16.1.tar.gz

# 安装依赖
yum install  pcre pcre-devel zlib zlib-devel openssl openssl-devel

# 配置
./configure [可以添加自己所需要的模块]

make & make install
# 检测是否安装成功
/usr/sbin/nginx -v
/usr/sbin/nginx -c /etc/nginx/nginx.conf
~~~



## yum 安装

```
yum list nginx
yum install nginx
nginx -v
systemctl start nginx
nginx -s reload
```

yum安装的东西会自动在/etc的文件目录下面

cd /etc/nginx



# 目录说明：

/etc/nginx/

- conf.d    #nginx主配置文件
  - default.conf
- nginx.conf
- fastcgi_params    #cgi配置相关，fastcgi配置
- scgi_params   #cgi配置相关，fastcgi配置
- uwsgi_param   #cgi配置相关，fastcgi配置
- koi-utf   #编码转换映射转化文件(很少用到)
- koi-win   #编码转换映射转化文件(很少用到)
- win-utf   #编码转换映射转化文件(很少用到)
- mime.types    #设置HTTP协议的Content-Type与拓展名对应关系
- moudules  #Nginx目录模块

