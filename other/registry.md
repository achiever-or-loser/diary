



~~~
docker pull docker.bintray.io/jfrog/artifactory-oss

docker run --name artifactory -d -p 8081:8081 -p 8082:8082 docker.bintray.io/jfrog/artifactory-oss
~~~







[maven repository](https://blog.csdn.net/dishen10/article/details/108969207)



















# Ques

I navigate to `localhost:8081/artifactory` and after the setup image it redirects to `localhost:8082/ui/` and shows page not found. Im not sure why the port changes and I have looked at the documentation for installing and there isnt anything about the port change. changing the port back to 8081 just shows a HTTP Status 404 – Not Found.



docker: Error response from daemon: driver failed programming external connectivity on endpoint artifactory (c8d4a9b870c1ecb022057ddd101cf1408b1210bd27dcd9615dc647d554144af6):  (iptables failed: iptables --wait -t nat -A DOCKER -p tcp -d 0/0 --dport 8082 -j DNAT --to-destination 172.17.0.3:8082 ! -i docker0: iptables: No chain/target/match by that name.
 (exit status 1)).

systemctl restart docker