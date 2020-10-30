# mongodb

```
docker run -itd --name mongo -p 27017:27017 mongo --auth
$ docker exec -it mongo mongo admin
# 创建一个名为 admin，密码为 123456 的用户。
>  db.createUser({ user:'admin',pwd:'admin',roles:[ { role:'userAdminAnyDatabase', db: 'admin'}]});
db.createUser({ user:'csc',pwd:'csc',roles:[ { role:'userAdminAnyDatabase'}]})
# 尝试使用上面创建的用户信息进行连接。
> db.auth('admin', '123456')
```

