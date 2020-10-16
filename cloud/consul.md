# consul

服务删除

[paas-portal-sit-9003](https://blog.csdn.net/v1/agent/service/deregister/paas-portal-sit-9003) （服务名称id）

curl [http://server_ip:8500/v1/agent/service/deregister/paas-portal-sit-9003](https://blog.csdn.net/v1/agent/service/deregister/paas-portal-sit-9003) -X PUT

节点删除

[4b36b27317a0](https://blog.csdn.net/v1/agent/force-leave/4b36b27317a0)（节点ID）

[http://server_ip:8500/v1/agent/force-leave/4b36b27317a0](https://blog.csdn.net/v1/agent/force-leave/4b36b27317a0)





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


