

**#!** 告诉系统其后路径所指定的程序即是解释此脚本文件的 Shell 程序。

echo 命令用于向窗口输出文本。



sh文件是否由执行权限；执行脚本时注意文件位置，运行其它二进制的程序也一样，直接写 test.sh，linux 系统会去 PATH 里寻找有没有叫 test.sh 的，而只有 /bin, /sbin, /usr/bin，/usr/sbin 等在 PATH 里，你的当前目录通常不在 PATH 里，所以写成 test.sh 是会找不到命令的，要用 ./test.sh 告诉系统说，就在当前目录找。



```
# 不需要在第一行指定解释器信息，写了也没用。
/bin/sh test.sh
```

# 定义变量

定义变量时，变量名不加美元符号，变量名和等号之间不能有空格，

- 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
- 中间不能有空格，可以使用下划线（_）。
- 不能使用标点符号。
- 不能使用bash里的关键字（可用help命令查看保留关键字）。



使用一个定义过的变量，只要在变量名前面加美元符号即可

使用 readonly 命令可以将变量定义为只读变量，只读变量的值不能被改变。

使用 unset 命令可以删除变量，不能删除只读变量。


## 变量类型
局部变量、环境变量、shell变量

字符串是shell编程中最常用的数据类型，字符串可以用单引号，也可以用双引号，也可以不用引号。

单引号字符串的限制：

- 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
- 单引号字串中不能出现单独一个的单引号（对单引号使用转义符后也不行），但可成对出现，作为字符串拼接使用。

双引号的优点：

- 双引号里可以有变量
- 双引号里可以出现转义字符
# 字符串
```
your_name="runoob"
# 使用双引号拼接
greeting="hello, "$your_name" !"
greeting_1="hello, ${your_name} !"
echo $greeting  $greeting_1
# 使用单引号拼接
greeting_2='hello, '$your_name' !'
greeting_3='hello, ${your_name} !'
echo $greeting_2  $greeting_3
```

输出结果为：

```
hello, runoob ! hello, runoob !
hello, runoob ! hello, ${your_name} !
```

字符串 indexof substring length

~~~
#! /bin/bash
string="runoob is a great site"
# 查找字符 i 或 o 的位置(哪个字母先出现就计算哪个)
echo `expr index "$string" io`
# 从字符串第 2 个字符开始截取 4 个字符
echo ${string:1:4} # 输出 unoo
# 字符串长度
echo ${#string}

~~~



# 数组

bash支持一维数组（不支持多维数组），并且没有限定数组的大小。

~~~
array_name=(value0 value1 value2 value3)
array_name[0]=value0
valuen=${array_name[n]}
echo ${array_name[@]}

# 取得数组元素的个数
length=${#array_name[@]}
# 或者
length=${#array_name[*]}
# 取得数组单个元素的长度
lengthn=${#array_name[n]}

~~~



多行注释

~~~
:<<'
 注释内容
'
~~~



# 传参

脚本内获取参数的格式为：**$n**。**n** 代表一个数字，1 为执行脚本的第一个参数，2 为执行脚本的第二个参数，以此类推。 **$0** 为执行的文件名（包含文件路径）

$* 与 $@ 区别：$* 以一个字符串显示所有入参；$@返回每个参数

# expr

expr 是一款表达式计算工具，使用它能完成表达式的求值操作，表达式需要被``包含（是反引号不是单引号）

乘号(*)前边必须加反斜杠(\)才能实现乘法运算

关系运算符： -eq、-ne、-gt、-lt、-ge、-le

布尔运算符：！、-o（或） 、-a（与）

逻辑运算符：&&、||

字符串运算符：

文件测试运算符：

echo、printf

test 检查某个条件成立

# if

~~~
if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi
if [ $(ps -ef | grep -c "ssh") -gt 1 ]; then echo "true"; fi

~~~

# for

~~~
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
for var in item1 item2 ... itemN; do command1; command2… done;
~~~

[ -d DIR ] 如果 FILE 存在且是一个目录则为真。

[ -n STRING ] 如果STRING的长度非零则为真 ，即判断是否为非空，非空即是真；

# while

~~~
while condition
do
    command
done
~~~

# case

~~~
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2）
    command1
    command2
    ...
    commandN
    ;;
*)
    command1
    command2
    command3
    ;;
esac
~~~





函数参数

函数返回值在调用该函数后通过 $? 来获得。

$10 不能获取第十个参数，获取第十个参数需要${10}。当n>=10时，需要使用${n}来获取参数。

输入输出重定向



外部脚本

~~~
. filename   # 注意点号(.)和文件名中间有一空格
# 或 包含文件代码
source filename
~~~



# $

~~~
$0 #当前脚本的文件名

$n #传递给脚本或函数的第n个参数

$# #传递给脚本或函数的参数的个数

$* #传递给脚本或函数的所有参数

$@ #传递给脚本或函数的所有参数

$? #上个命令的退出状态或函数的返回值

$$ #当前Shell进程ID。对于 Shell 脚本，就是这些脚本所在的进程ID。
~~~



$* 和 $@ 的区别

$* 和 $@ 都表示传递给函数或脚本的所有参数，不被双引号(" “)包含时，都以”$1" “2 &quot; … &quot; 2&quot; … &quot;2"…"n” 的形式输出所有参数

当它们被双引号(" “)包含时，”$*" 会将所有的参数作为一个整体，以"$1 $2 … n &quot; 的 形 式 输 出 所 有 参 数 ； &quot; n&quot;的形式输出所有参数；&quot;*n*"的形式输出所有参数；"@" 会将各个参数分开，以"$1" “2 &quot; … &quot; 2&quot; … &quot;2"…"n” 的形式输出所有参数。



# 将命令的结果赋值给变量

variable=`command`

variable=$(command)

第一种方式把命令用反引号`` ``（位于 Esc 键的下方）包围起来，反引号和单引号非常相似，容易产生混淆，所以不推荐使用这种方式；第二种方式把命令用`$()`包围起来，区分更加明显，所以推荐使用这种方式。

















