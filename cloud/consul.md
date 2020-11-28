# consul

服务删除(具体的实例)

[paas-portal-sit-9003](https://blog.csdn.net/v1/agent/service/deregister/paas-portal-sit-9003) （服务实例id）

curl [http://server_ip:8500/v1/agent/service/deregister/paas-portal-sit-9003](https://blog.csdn.net/v1/agent/service/deregister/paas-portal-sit-9003) --header "X-Consul-Token: aad1c625-f959-008b-5621-d68600d4e6a7" -X PUT

curl [http://localhost:8500/v1/agent/service/deregister/paas-portal-sit-9003](https://blog.csdn.net/v1/agent/service/deregister/paas-portal-sit-9003) --header "X-Consul-Token: aad1c625-f959-008b-5621-d68600d4e6a7" -X PUT

curl http://localhost:8500/v1/agent/service/deregister/6a5c71c7-d8e8-4cf2-8c77-df6ccfd84125 --header "X-Consul-Token: 4a889de0-89b8-2b94-dc1f-537d8c4e23ea" -X PUT



4a889de0-89b8-2b94-dc1f-537d8c4e23ea

节点删除

[4b36b27317a0](https://blog.csdn.net/v1/agent/force-leave/4b36b27317a0)（节点ID）

http://server_ip:8500/v1/agent/force-leave/4b36b27317a0



server-client模式：减轻server是压力；server挂掉，节点下的服务全部无效



## acl

~~~
{
    "datacenter":"tencent-datacenter",
    "data_dir":"/usr/local/consul-1.6.2/data",
    "log_file":"/usr/local/consul-1.6.2/log/",
    "log_level":"INFO",
    "bind_addr":"127.0.0.10",
    "client_addr":"127.0.0.1",
    "node_name":"tencent-node",
    "ui":true,
    "bootstrap_expect":1,
    "server":true,
    "acl":{
        "enabled":true,
        "default_policy":"deny",
        "enable_token_persistence":true,
        "enable_key_list_policy":true
    }
}
~~~

启动Consul：./consul agent -config-file=config-acl.json

初始化Consul ACL的命令 consul acl bootstrap

read、write、deny、list

[`list`](https://www.consul.io/docs/acl/acl-rules.html#list)：允许访问Consul KV中段下的所有键。请注意，此策略只能与`key_prefix`资源一起使用，并且[`acl.enable_key_list_policy`](https://www.consul.io/docs/agent/options.html#acl_enable_key_list)必须设置为true。

"enable_key_list_policy":true

服务策略

~~~
ervice_prefix "" {
  policy = "write"
}
~~~

kv策略

~~~
key_prefix "" {
 policy = "list"
}
key_prefix "" {
 policy = "write"
}
key_prefix "config/" {
 policy = "read"
}
~~~





## 集群

单台启动多个服务注意端口号

~~~
"start_join":[
        "10.211.55.25",
        "10.211.55.26"
    ],
    "retry_join":[
        "10.211.55.25",
        "10.211.55.26"
    ],
~~~



![server and client](http://static.bluersw.com/images/spring-cloud-consul-client-25.png)

只使用Consul的Server模式有以下2个问题：

- 因为Consul Server数量受到控制所以压力承载（扩展性）是个问题。
- Server很少导致一个Server下会注册很多微服务，当Server挂掉，这个Server节点下注册的微服务都会视为无效。



































