# Docker 安装

[https://www.cnblogs.com/lylsr/p/11173003.html]()

1. 旧版的docker叫做docker或docker-engine,现在已经停用了，如果有老版本的，先卸载掉

~~~
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
~~~

2. 安装需要的软件包，yum-utils提供yum配置管理器实用程序，设备映射程序存储驱动程序需要持久数据和lvm2

~~~
$ sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
~~~

3. 设置源仓库地址

```
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

如果慢的话，可以换阿里的源试试

```Shell
sudo yum-config-manager\
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

4. 安装docker-ce最新版

`$ sudo yum install docker-ce docker-ce-cli containerd.io`

如果要安装指定版本, 查看所有仓库中所有docker版本，并选择特定版本安装

```
 $ yum list docker-ce --showduplicates | sort -r
 docker-ce.x86_64  3:18.09.1-3.el7                     docker-ce-stable
 docker-ce.x86_64  3:18.09.0-3.el7                     docker-ce-stable
 docker-ce.x86_64  18.06.1.ce-3.el7                    docker-ce-stable
 docker-ce.x86_64  18.06.0.ce-3.el7                    docker-ce-stable
 $ sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
```

5. 启动 Docker

`$ sudo systemctl start docker`

6. 验证 Docker CE 是否成功安装，我们可以跑个hello-world镜像

~~~
$ docker version#有client和service两部分表示docker安装启动都成功
$ sudo run hello-world
~~~


## 镜像

~~~
docker images
docker push ip:port/image:tag
docker pull ip:port/image:tag
docker search imageName
docker rmi imageName
docker commit 
docker build name:tag ./Dockerfile
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
~~~

镜像重命名

~~~
docker tag imageId newName
docker rmi oldName # 注意tag后两个imageId相同
~~~



## 容器

~~~
#e入参;--restart容器的重启策略;i交互式,t终端,d后台运行
docker run -e "SPRING_PROFILES_ACTIVE=dev" --restart=always -itd --name containName -p outPort:inPort -v outPath:inPath image:tag
docker ps -a
docker start/restart id
docker stop id
docker rm -f id

docker exec	-it id bin/bash
docker build name:tag ./Dockerfile

docker export id > xxx.tar
docker import URL
cat xxx.tar | docker import - xxx:v1

~~~

将容器containerId保存为新的镜像,并添加提交人信息和说明信息

docker commit -m 'message' -a ='author' <containerId> repository:tag



获取容器/镜像的元数据（镜像、状态、host等）`docker inspect 容器id `

### 容器重启

容器的重启策略：

- no，默认策略，在容器退出时不重启容器
- on-failure，在容器非正常退出时（退出状态非0），才会重启容器
- on-failure:3，在容器非正常退出时重启容器，最多重启3次
- always，在容器退出时总是重启容器
- unless-stopped，在容器退出时总是重启容器，但是不考虑在Docker守护进程启动时就已经停止了的容器

容器退出的状态码

- 0，表示正常退出
- 非0，表示异常退出（退出状态码采用chroot标准）
- 125，Docker守护进程本身的错误
- 126，容器启动后，要执行的默认命令无法调用
- 127，容器启动后，要执行的默认命令不存在
- 其他命令状态码，容器启动后正常执行命令，退出命令时该命令的返回状态码作为容器的退出状态码

通过--restart选项，可以设置容器的重启策略，--restart选项通常只用于detached模式的容器，--restart选项不能与--rm选项同时使用。显然，--restart选项适用于detached模式的容器，而--rm选项适用于foreground模式的容器。

### attach与exec

- docker exec: 在已运行的容器中，执行命令，操作对象是容器，如果你要进入已运行的容器，并且执行命令，用exec；
- docker attach: 同样操作的是已运行的容器，可以将本机标准输入（键盘输入）输到容器中，也可以将容器的输出显示在本机的屏幕上，如果你想查看容器运行过程中产生的标准输入输出，用attach；





[Docker 网络](https://www.jianshu.com/p/d84cdfe2ea86)



/var/lib/docker/ 目录



# Dockerfile

### COPY

复制指令，从上下文目录中复制文件或者目录到容器里指定路径。

```
COPY [--chown=<user>:<group>] <源路径1>...  <目标路径>
```

### ADD

ADD 指令和 COPY 的使用格式一致（同样需求下，官方推荐使用 COPY）。功能也类似，不同之处如下：

- ADD 的优点：在执行 <源文件> 为 tar 压缩文件的话，压缩格式为 gzip, bzip2 以及 xz 的情况下，会自动复制并解压到 <目标路径>。
- ADD 的缺点：在不解压的前提下，无法复制 tar 压缩文件。会令镜像构建缓存失效，从而可能会令镜像构建变得比较缓慢。具体是否使用，可以根据是否需要自动解压来决定。

### CMD

类似于 RUN 指令，用于运行程序，但二者运行的时间点不同:

- CMD 在docker run 时运行。
- RUN 是在 docker build。

作用：为启动的容器指定默认要运行的程序，程序运行结束，容器也就结束。CMD 指令指定的程序可被 docker run 命令行参数中指定要运行的程序所覆盖。

注意：如果 Dockerfile 中如果存在多个 CMD 指令，仅最后一个生效。

### ENTRYPOINT

类似于 CMD 指令，但其不会被 docker run 的命令行参数指定的指令所覆盖，而且这些命令行参数会被当作参数送给 ENTRYPOINT 指令指定的程序。

但是, 如果运行 docker run 时使用了 --entrypoint 选项，此选项的参数可当作要运行的程序覆盖 ENTRYPOINT 指令指定的程序。

**优点**：在执行 docker run 的时候可以指定 ENTRYPOINT 运行所需的参数。

**注意**：如果 Dockerfile 中如果存在多个 ENTRYPOINT 指令，仅最后一个生效。

可以搭配 CMD 命令使用：一般是变参才会使用 CMD ，这里的 CMD 等于是在给 ENTRYPOINT 传参，以下示例会提到。

### ENV

设置环境变量，定义了环境变量

### ARG

构建参数，与 ENV 作用一至。不过作用域不一样。ARG 设置的环境变量仅对 Dockerfile 内有效，也就是说只有 docker build 的过程中有效，构建好的镜像内不存在此环境变量。

### VOLUME

定义匿名数据卷。在启动容器时忘记挂载数据卷，会自动挂载到匿名卷。

作用：

- 避免重要的数据，因容器重启而丢失，这是非常致命的。
- 避免容器不断变大。

### EXPOSE

仅仅只是声明端口。

作用：

- 帮助镜像使用者理解这个镜像服务的守护端口，以方便配置映射。
- 在运行时使用随机端口映射时，也就是 docker run -P 时，会自动随机映射 EXPOSE 的端口。

### WORKDIR

指定工作目录。用 WORKDIR 指定的工作目录，会在构建镜像的每一层中都存在。（WORKDIR 指定的工作目录，必须是提前创建好的）。

docker build 构建镜像过程中的，每一个 RUN 命令都是新建的一层。只有通过 WORKDIR 创建的目录才会一直存在。

# Docker-Compose

[官方文档](https://docs.docker.com/compose/install/)

Docker Compose 是用于定义和运行多容器 Docker 应用程序的工具。可以使用 YML 文件配置应用程序需要的所有服务,然后使用命令从 YML 文件配置中创建并启动所有服务。


对于`alpine`，需要以下依赖包：`py-pip`、`python-dev`、`libffi-dev`、`openssl-dev`、`gcc`、`lib-dev`、`make`

下载Docker-Compose文件

`sudo curl -L "https://github.com/docker/compose/releases/download/1.25.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose`

> PS:如果下载速度慢，可尝试换别的源下载

```
sudo curl -L https://get.daocloud.io/docker/compose/releases/download/1.25.4/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose`
```

给文件添加执行权限

`sudo chmod +x /usr/local/bin/docker-compose`

验证

`docker-compose --version`

可能需要手动添加软连接

`sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose`

## registry

[registry 官方](https://docs.docker.com/registry/deploying/)



```
docker run -d -p 5000:5000 --restart=always --name registry \
	-e REGISTRY_HTTP_ADDR=0.0.0.0:5000 \
	-v /home/images:/var/lib/registry \
	registry:2.7.1
```

- -d后台运行进程
- -p 5000:5000宿主机和docker容器端口号映射，前一个5000表示宿主机端口号，后一个表示docker容器端口号
- --restart =always表示docker容器重启后容器自动启动
- -e REGISTRY_HTTP_ADDR=0.0.0.0:5000环境变量，用来更改容器监听的端口号
- -v /home/images:/var/lib/registry自定义镜像存储位置，/var/lib/registry是容器中默认存储镜像的位置，/home/images是自定义文件系统的路径
- registry:2.7.1 registry是镜像名称，2.7.1是镜像版本号

> 注意：此示例仅用于测试环境，生产环境的registry必须通过TLS验证，理想情况下应该使用访问控制机制



push到私有仓库

1、登录

docker login http://xxxxx.com

2、登录私有hub创建项目

例如项目叫：abc-dev

2、给镜像打tag

docker tag 2e25d8496557 xxxxx.com/abc-dev/arc:1334

2e25d8496557：IMAGE ID，可以用docker images 查看

xxxxx.com：私有hub域名

abc-dev：项目名称

arc：镜像名称

1334：镜像版本号

4、推送

docker push xxxxx.com/abc-dev/arc:1334



# 原理

docker cmd entrypoint

底层技术libContainer、libnetwork、

docker 卷 volume 、多个文件系统（只读层叠加），启动容器时在对应的只读层上加一个读写层，如果运行的容器修改一个文件，改文件会从只读层复制到读写层，只读版本仍然存在，被读写层隐藏，。删除容器重新运行时之前修改会丢失。

容器通过网桥去连接宿主机从而访问外网。统一网桥上的容器可以通过内网IP互相访问，容器重启会重新分配IP。





# 问题

docker: Error response from daemon: driver failed programming external connectivity on endpoint rabbit (23226cc37e041918194494612e60ef1a3b5e42f430355c5b7ddb66ebd0a157fd):  (iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 61613 -j DNAT --to-destination 172.17.0.5:61613 ! -i docker0: iptables: No chain/target/match by that name.
 (exit status 1)).

systemctl restart docker

 10.205.104.109



docker引入私服仓库后

Get https://xx.xx.xx.xx:5000/v2/: http: server gave HTTP response to HTTPS client

/etc/docker/daemon.json文件中添加不安全的registry注册表地址（此种方案只适合在测试环境使用，生产环境要使用TLS证书的方式）

```
{"insecure-registries":["xx.xx.xx.xx:5000"]}
```

重新启动docker：service docker restart

