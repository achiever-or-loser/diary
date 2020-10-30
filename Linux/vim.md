# vim

vim编辑器需要安装三个包：vim-enhanced-版本、vim-minimal-版本、vim-common-版本

```
rpm -qa|grep vim # 查看已经安装的包，确认VIM是否已经安装
yum -y install vim-enhanced # 安装缺少的某个
yum -y install vim* # 安装所有vim需要的
```



1,10d # 删除1-10行

ndd # 从当前行删除n行

dd

yy p # 复制粘贴

6,9 co 12 # 复制第6行到第9行之间的内容到第12行后面

u<---->Ctrl+R # 撤销、反撤销

可视模式是选中一块区域进行操作，包括删除，替换，复制，粘贴，改变大小写等。在正常模式下按v（小写）进入字符文本，按V（大写）进入行文本，按ctrl+v进入块文本。然后使用上下左右键进行操作选中区域。对选中的部分使用d进行删除，y进行复制，p进行粘贴，r进行文本替换，gu转换为小写，gU转换为大写，g~大小写互换。



ggVG

dG

ggyG ???

执行 vim --version
在列出来的feature中 xterm_clipboard 前面得有 ‘+’ 加号，才具有这个功能。或者使用gui版本的vim。

gg"*yG

gg"+yG

