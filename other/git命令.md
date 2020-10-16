# git 命令

## 初始化配置

1. 下载安装后在 git bash 中配置全局的 `name` 和 `email`

~~~
$ git config --global user.name "name"
$ git config --global user.email xxx@xx.com
~~~

2. 生成公钥

~~~
$ ssh-keygen -t rsa -C 'xxx@xx.com'
~~~

rsa 为密钥的类型

在生成公钥过程中需要Enter三次，第一次可以自定义生成的文件的位置，也可以采用默认的（用户目录 .ssh 下）；

接着是输入两次密码（该密码是你push文件的时候要输入的密码，而不是github管理者的密码），当然，你也可以不输入密码，直接按回车（这样push的时候就不需要输入密码，直接提交到github上了）；

最后windows下默认会会在用户目录 .ssh 下生成id_rsa 和id_rsa.pub 文件

3. 在Github或Gitlab上传密钥，setting中找到ssh并添加把第二步中生成的id_rsa.pub中的内容粘贴，title随意。
4. 在git bash中测试

~~~
$ ssh-keygen -t rsa -C 'xxx@xx.com'
$ ssh-keygen -v rsa -C 'xxx@xx.com' # debug
~~~

### 问题

1. 私服的地址、端口等配置

新建config文件，可以配置自己服务器的IP、端口、公钥的位置。

~~~
Host 172.30.64.81
Port 45685
IdentityFile ~/.ssh/id_rsa
~~~

配置host文件







而 git stash apply，会将当前分支的最后一次缓存的内容释放出来，但是刚才的记录还存在list中

而 git stash pop，也会将当前分支的最后一次缓存的内容释放出来，但是刚才的记录不存在list中











































