---
title: 工作中常用的 Shell 命令及技巧
date: 2019-04-14 15:23:23
tags: [Linux,Shell]
categories: Linux
keywords:
 - Linux
 - shell
 - commands
---

## 调试 bash 脚本的技巧

- 加 `-x` 参数运行 bash 脚本时，会显示执行的语句

```shell
# 也可以在 demo.sh 中加上 set -x
bash -x demo.sh
```

- 设置环境变量，然后通过如上方式运行脚本时，会显示行号

```shell
export PS4='+${BASH_SOURCE}:${LINENO}:${FUNCNAME[0]}: '
```

<!-- more -->

参考

- [耗子叔-如何调试BASH脚本](https://coolshell.cn/articles/1379.html/comment-page-1#comment-1965637

## 快速输入历史命令

输入关键词之后，连续使用`ctrl+r`快速切换

- [In reverse-i-search (Ctrl+R ) ,Any method to switch between similar commands in history](https://stackoverflow.com/questions/14231643/in-reverse-i-search-ctrlr-any-method-to-switch-between-similar-commands-in)
- [linux – 在反向搜索(Ctrl R),任何在历史记录中类似命令之间切换的方法](https://codeday.me/bug/20170919/73974.html)

`Ctrl+p/n` 向上/向下 显示缓存命令

- [Ubuntu终端常用的快捷键](http://www.cnblogs.com/nucdy/p/5251659.html)

## ssh远程执行命令

需要远程到其他节点上执行一些 shell 命令，示例：

```shell
#! /bin/sh
set -x

ssh root@192.168.3.43 > /dev/null 2>&1 << EOF
cd /tmp
touch test.txt
exit
EOF
echo done
```

参考：

- [简书-【ssh】ssh远程执行命令](https://www.jianshu.com/p/25c2407f5905)

## for循环

```shell
echo "Start Datetime:" $(date +"%Y-%m-%d %H:%M:%S")
for i in {1..3}
do
  echo $i
  git pull --all
  if [ $? -ne 0 ]
  then
    echo "不成功"
    # 睡眠2s
    sleep 2
  else
    break
  fi
done
echo "End Datetime:" $(date +"%Y-%m-%d %H:%M:%S")
```

参考：

- [Linux下Shell的for循环语句](https://www.cnblogs.com/EasonJim/p/8315939.html)
- [Shell脚本中的分号使用](https://www.cnblogs.com/EasonJim/p/8315896.html)

## if 判断

判读字符串($str)是否包含另一个字符串($str1)：

方法1：

```shell
if [  `echo $str | grep -e '$str1'`  ] ; then
    echo yes
fi
```

方法2(如果`$str1`在判断中直接使用字符串而不是变量，则不能加引号，如`if [[ $str =~ ^dx ]]`判读字符串`$str`是否以`dx`开头，`^dx`不能加引号)：

```shell
if [[ $str =~ $str1 ]] ; then
    echo yes
fi
```

比较两个字符串是否相等的办法是：

```shell
    if [ "$test"x = "test"x ]; then
```

这里的关键有几点：

- 使用单个等号，我发现，2个等号也 OK；
- 注意到等号两边各有一个空格：这是unix shell的要求
- 注意到`"$test"x`最后的`x`，这是特意安排的，因为当$test为空的时候，上面的表达式就变成了`x = testx`，显然是不相等的。而如果没有这个 `x`，表达式就会报错：`[: =: unary operator expected`

参考：

- [shell中if做比较](https://www.cnblogs.com/276815076/archive/2011/10/30/2229286.html)

## 从URL截取字段

默认去除url的最后斜线：

```shell
url=http://xxx/patch/xxx/xxxx/
tar_name=$(echo ${url%*/}|awk -F '/' '{print $NF}')
```

## shell 判断字符串包含

```shell
if [[ $tar =~ tar.gz ]];then echo "包含";fi
```

- [Shell判断字符串包含关系的几种方法](https://www.cnblogs.com/willhua/articles/6141046.html)

## Bash 加 `-xe` 表示什么意思

`-e` 使shell立即退出，某些东西会返回一个错误(这通常在shell脚本中用作故障保护机制),
`-x` 允许详细执行脚本，你可以看到发生了什么

- [将-xe参数传递给/ bin / bash的做法是什么](https://codeday.me/bug/20181105/355713.html)

## Shell 中的引号

https://blog.csdn.net/miyatang/article/details/8077123

## Shell定义变量和给变量赋值

### 将命令的执行结果赋值给变量

```
var='pwd'
# 或者
var=$(pwd)
```

### 将 Bash 的内置命令 read 读入的内容赋值给变量

```
echo -n "Enter var:";read var
```

## 通配符与特殊符号

| 符号 | 意义                                                                                                     |
| ---- | -------------------------------------------------------------------------------------------------------- |
| *    | 代表0个到无穷多个任意字符                                                                                |
| ？   | 代表一定有一个任意字符                                                                                   |
| []   | 同样是代表一定有一个在中括号内的字符（非任意字符）。例如，[abcd]代表一定有一个字符，可能是这四个中的一个 |
| [-]  | 若减号在括号内，代表在编码顺序内的所有字符。例如，[0-9]代表0-9之间所有数字，因为数字的语系编码是连续的   |
| [^]  | 若中括号第一个字符是`^`，表示原向选择，例如[^abc]代表一定有一个字符，只要是非a,b,c的其他字符就接收       |

### 示例

- 找出`/etc/`目录下文件夹名字刚好有5个字母的文件名：`ll -d /etc/????? `
- 找出`/etc/`下面文件名含有数字的的文件名：`ll -d /etc/*[0-9]*`
- 找出`/etc/`下面文件名开头非小写字母的文件名：`ll -d /etc/[^a-z]*`
- 将上面例子找到的文件复制到`/tmp`中：`

### bash 中的特殊符号

| 符号  | 内容                                            |
| ----- | ----------------------------------------------- |
| \#    | 注释符号                                        |
| \\    | 转义符号，将特殊字符或通配符还原成一般字符      |
| 竖线  | 管道                                            |
| ;     | 连续命令执行分隔符，连续命令的界定              |
| ~     | 用户的主文件夹                                  |
| $     | 使用变量的前导符                                |
| &     | 作业控制，将命令变成背景下工作                  |
| !     | 逻辑运算意义上的“非”                            |
| \>,>> | 数据流重定向，输出导向，分别是“替换”与“累加”    |
| <,<<  | 数据流重定向，输入导向                          |
| ''    | 单引号，**不具有变量置换的功能**                |
| ""    | **具有变量置换的功能**                          |
| \`\`  | 两个重音符中间为可以先执行的命令，也可以使用$() |
| ()    | 中间为子shell的起始与结束                       |
| {}    | 中间为命令块的组合                              |

## 给Linux新增硬盘之后的操作

```shell
fdisk -l #查看磁盘情况，发现有磁盘没有分区，比如是/dev/xvde
fdisk /dev/xvde #进行磁盘分区的操作
```

`fdisk`磁盘分区时，可以输入`m`，会有提示：
```shell
Command (m for help): m
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition
   l   list known partition types
   m   print this menu
   n   add a new partition
   o   create a new empty DOS partition table
   p   print the partition table
   q   quit without saving changes
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit
   x   extra functionality (experts only)
```

我依次这么输入的：

```shell
add a new partition:n
partion type:p
Partion number:分区个数 1
First sector默认值:Enter
Lase sector默认值:Enter
print the partion table:p
write table to disk and exit:w
```

分区完成之后，需要格式化

```shell
mkfs -t ext4 /dev/xvde1
```

将新建分区挂载到/data目录下：

```
mkdir /data
mount /dev/xvde /data
```

挂载完毕，输入`df -hT`可以查看到新建的分区

设置文件系统的自动挂载
```
vi /etc/fstab
```

添加`/dev/xvde1 /data ext4 defaults 0 1`

在 fstab 配置文件中加入挂载点之后其实就已经是开机自动挂载了，不需要用 mount 命令挂载。但是还是用 mount  实在，因为最后还是需要写入`/etc/fstab`

第一段可以用分区名，也可以用 `blkid` 的方式获取 `UUID` 的值

参考：

- www.jianshu.com/p/7b8c3509d5fe
- [玩转 Linux 之：磁盘分区、挂载知多少？](https://my.oschina.net/leejun2005/blog/290073)
- [LVM](https://wiki.archlinux.org/index.php/LVM_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
- [Linux 磁盘管理](http://wiki.jikexueyuan.com/project/linux/disk-management.html)

## 备份原有配置文件

只将不带注释的内容提取出来，作为配置文件

```shell
mv /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd.conf_bak
grep -v "#" /etc/vsftpd/vsftpd.conf_bak > /etc/vsftpd/vsftpd.conf
```

-v 参数表示反选

## 修改主机名

- [Linux下修改主机名hostname](http://yanue.net/post-142.html)

## 查看Linux系统版本

```
lsb_release -a # 适用于所有Linux系统
cat /etc/os-release #推荐
cat /etc/redhat-release # 仅适用于Redhat系列的Linux系统
uname -a # 查看Linux内核
cat /proc/version # 查看Linux内核
```

- [Linux 命令行：查看系统版本的几种方法](http://www.codebelief.com/article/2017/02/check-system-version-on-linux/)
- [Linux查看安装的系统版本](https://blog.csdn.net/u011669700/article/details/79443134)

## 显示 shell 执行过程

```
set -x
```

如果想隐藏某一行的内容：

```shell
set -x
xxxx
set +x
yyyy
set -x
```

- [Linux 查看脚本的执行过程](https://blog.csdn.net/doiido/article/details/43966419)
- [Shell脚本调试技术](https://www.ibm.com/developerworks/cn/linux/l-cn-shell-debug/index.html)

## 查看端口占用

```shell
[root@centos7.4 software]# netstat -apn                查看当前运行的所有进程的端口使用情况
[root@centos7.4 software]# netstat -apn | grep 端口号   查看指定端口使用情况
[root@centos7.4 software]# kill 指定端口的pid号          杀死指定进程（端口号对应的pid）
```

## 打印第X行

```shell
head -n X | tail -n 1
```

## 显示 Path 环境变量

显示你的环境变量PATH，一个目录一行：

```shell
echo $PATH | tr : \\n
```

## atime mtime ctime 的含义

- atime (access time) 访问时间，表示文件最后被访问的时间；
- mtime (modify time) 修改时间，文件内容被修改的最后时间，平常我们 `ls -l` 查看文件是，显示的就是 mtime；
- ctime (change time) 变化时间，文件的元数据发生变化的时间，例如权限、所有者等，通俗来讲，就是文件属性或文件位置改动的时间；

利用 `stat file_name` 可以查看文件 「amc」time 。

```shell
echo "hello" >> issue
```

写文件操作（`>>` 方式）不会导致 atime(访问时间）的修改，但是 mtime 和 ctime 会发生修改。mtime 修改了我们可以理解的，毕竟我们修改了文件的，

那为何ctime也修改了呢， 仔细可以发现我们文件的大小发生了变化，也就是元数据发生了变化，所以ctime也是要变化的

参考：
- [linux中文件的三种time（atime,mtime,ctime）](https://www.cnblogs.com/zhaojiedi1992/p/zhaojiedi_linux_031_linuxtime.html#commentform)

## 重定向相关

- [CSDN-Linux里的2>&1究竟是什么](https://blog.csdn.net/ggxiaobai/article/details/53507530)

## 防火墙

有时候需要服务器上需要打开防火墙的端口：

```shell
sudo firewall-cmd --zone=public --add-port=60001/udp --permanent
sudo firewall-cmd --reload
#之后检查新的防火墙规则
firewall-cmd --list-all
```

由于只是用于开发环境，所以打算把防火墙关闭掉

```shell
//临时关闭防火墙,重启后会重新自动打开
systemctl restart firewalld
//检查防火墙状态
firewall-cmd --state
firewall-cmd --list-all
//Disable firewall
systemctl disable firewalld
systemctl stop firewalld
systemctl status firewalld
//Enable firewall
systemctl enable firewalld
systemctl start firewalld
systemctl status firewalld
```

参考：

- [CentOS 7开放端口和关闭防火墙](https://www.jianshu.com/p/bad33004bb4f)

## shell echo 输出带颜色

输出格式

- 设置底色，字体颜色：`echo -e "\033[底色;字体颜色m 内容 \033[0m"`
- 设置字体颜色：`echo -e "\033[字体颜色m 内容 \033[0m"`

其中 `\033` 是 ESC 健的八进制，`\033[`即告诉终端后面是设置颜色的参数，显示方式，前景色，背景色均是数字

| 字体颜色 | 背景色 | 颜色   |
| -------- | ------ | ------ |
| 30       | 40     | 黑色   |
| 31       | 41     | 红色   |
| 32       | 42     | 绿色   |
| 33       | 43     | 黃色   |
| 34       | 44     | 蓝色   |
| 35       | 45     | 紫红色 |
| 36       | 46     | 青蓝色 |
| 37       | 47     | 白色   |

> 但有一点要注意，如果输出带颜色的字符后并没有恢复终端默认设置，后续的命令输出仍旧会采用之前的颜色，如果是在脚本中设置了颜色而未恢复，则整个脚本的输出都会采用之前的颜色，因此如果不希望影响后面文字的输出，最好是在输出带颜色的文字之后恢复终端默认设置，`\033[0m` 关闭所有属性

示例：

```shell
# 字颜色为绿色
echo -e "\033[32m download new package (version $version) \033[0m"
# 红底黑字
echo -e "\033[41;37mMichael翔\033[0m"
```

参考：

- [shell脚本中echo显示内容带颜色](https://www.cnblogs.com/lr-ting/archive/2013/02/28/2936792.html)

## 切割大文件与合并压缩文件

```shell
# 切割
split -b 90m -d janusgraph-0.4.0-hadoop2.zip janusgraph-0.4.0-hadoop2-split.
split -b 90m -d jdk-8u232-linux-x64.tar.gz jdk-8u232-linux-x64-split.

# 合并
cat janusgraph-0.4.0-hadoop2-split* >> janusgraph-0.4.0-hadoop2.zip
cat jdk-8u232-linux-x64-split* >> jdk-8u232-linux-x64.tar.gz
```

## FAQ

### Q:Linux各目录的作用

- [linux各文件夹的作用](http://www.cnblogs.com/amboyna/archive/2008/02/16/1070474.html)
- [/usr 目录结构](http://yijiebuyi.com/blog/c3f2a606f1f0bc098f02621dfcc8405b.html)
- [Unix目录结构的来历](http://www.ruanyifeng.com/blog/2012/02/a_history_of_unix_directory_structure.html)

### Q：http://blog.csdn.net/u011109356/article/details/54928955
`/dev/xvda1` 占满

## Linux 好文

- [Linux工具快速教程](https://linuxtools-rst.readthedocs.io/zh_CN/latest/index.html#)
- [glmapper-shell 脚本简单归纳和实践](http://www.glmapper.com/2019/08/01/shell-record/)

## 资源推荐

- [Github-命令行的艺术](https://github.com/jlevy/the-art-of-command-line/blob/master/README-zh.md)