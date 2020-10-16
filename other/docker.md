docker cmd entrypoint

底层技术libContainer、libnetwork、

docker 卷 volume 、多个文件系统（只读层叠加），启动容器时在对应的只读层上加一个读写层，如果运行的容器修改一个文件，改文件会从只读层复制到读写层，只读版本仍然存在，被读写层隐藏，。删除容器重新运行时之前修改会丢失。

容器通过网桥去连接宿主机从而访问外网。统一网桥上的容器可以通过内网IP互相访问，容器重启会重新分配IP。





镜像重命名

~~~
docker tag imageId newName
docker rmi oldName # 注意tag后两个imageId相同
~~~



docker: Error response from daemon: driver failed programming external connectivity on endpoint rabbit (23226cc37e041918194494612e60ef1a3b5e42f430355c5b7ddb66ebd0a157fd):  (iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 61613 -j DNAT --to-destination 172.17.0.5:61613 ! -i docker0: iptables: No chain/target/match by that name.
 (exit status 1)).

systemctl restart docker

 