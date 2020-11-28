# &&、()、||

command1 && command2

&&左边的command1执行成功(返回0表示成功)后，&&右边的command2才能被执行。

command1 || command2

如果||左边的command1执行失败(返回1表示失败)，就执行&&右边的command2。

(command1；command2；command3;...)

执行多个命令，则需要用命令分隔符分号隔开每个命令

# curl

https://www.cnblogs.com/hujiapeng/p/8470099.html

保存访问的网页

测试返回值

代理服务器和端口

cookie、response中header

模仿浏览器

下载（循环、分块、ftp）、上传文件



# sed

```
sed的命令格式：sed [options] 'command' file(s);
sed的脚本格式：sed [options] -f scriptfile file(s);
```

```
-e ：直接在命令行模式上进行sed动作编辑，此为默认选项;
 -f ：将sed的动作写在一个文件内，用–f filename 执行filename内的sed动作;
 -i ：直接修改文件内容;
 -n ：只打印模式匹配的行；
 -r ：支持扩展表达式;
```

```
s 替换指定字符;
g 表示行内全面替换;
```

```
sed -i 's/book/books/g' file
```

# eval

**eval command-line**

其中command－line是在终端上键入的一条普通命令行。然而当在它前面放上eval时，其结果是shell在执行命令行之前扫描它两次。如：

pipe="|"

eval ls $pipe wc -l

shell第1次扫描命令行时，它替换出pipe的值｜，接着eval使它再次扫描命令行，这时shell把｜作为管道符号了。

# lsof

查看进程开打的文件，打开文件的进程，进程打开的端口(TCP、UDP)

~~~
yum install lsof 
# 占用8080端口的进程
lsof -i:8080
~~~

| 选项       | 描述                                             |
| ---------- | ------------------------------------------------ |
| -a         | 列出打开文件存在的进程；                         |
| -c<进程名> | 列出指定进程所打开的文件；                       |
| -g         | 列出GID号进程详情；                              |
| -d<文件号> | 列出占用该文件号的进程；                         |
| +d<目录>   | 列出目录下被打开的文件；                         |
| +D<目录>   | 递归列出目录下被打开的文件；                     |
| -n<目录>   | 列出使用NFS的文件；                              |
| -i<条件>   | 列出符合条件的进程。（4、6、协议、:端口、 @ip ） |
| -p<进程号> | 列出指定进程号所打开的文件；                     |
| -u         | 列出UID号进程详情；                              |
| -h         | 显示帮助信息；                                   |
| -v         | 显示版本信息。                                   |

