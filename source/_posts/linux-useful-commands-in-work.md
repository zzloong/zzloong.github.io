---
title: 工作中常用的 Linux 命令
tags: [Linux,Shell]
toc: true
date: 2018-07-05 19:31:27
categories: Linux
---

## awk

示例：

env变量值如下，需要获得pkg_url的链接值：

```
{"name": "michael", "sex": "male", "pkg_url": "www.github.com", "number": "888"}
```

```
pkg_url=$(echo $env | awk -F "pkg_url\": \"" '{print $2}' | awk -F "\"," '{print $1}')
echo $pkg_url
www.github.com
```

-F 指定分隔规则，因为分隔规则中包含了双引号，所以需要用转义符号。

```
# 格式
$ awk 动作 文件名

# 示例
$ awk '{print $0}' demo.txt

echo 'hello:michael:xiang'|awk -F ':' '{print $1}'
```

<!-- more -->

- [awk print函数](https://blog.csdn.net/xiyangyang052/article/details/44926793)
- [printf+awk完美结合](http://blog.51cto.com/cfwlxf/1309242)
- [AWK 简明教程](https://coolshell.cn/articles/9070.html)
- [awk 入门教程](http://www.ruanyifeng.com/blog/2018/11/awk.html#comment-text)

## basename

basename命令用于打印目录或者文件的基本名称

```
[root@HGH1000059721 test]# basename a.tar .tar  #后缀：可选参数，指定要去除的文件后缀字符串。
a
[root@HGH1000059721 test]# basename /tmp/test/a.tar  #不带后缀，获取文件名
a.tar
```

参考：

- [basename](http://man.linuxde.net/basename)

## cp

将目录`src`复制到`dest`目录下，复制好后，`dest/src`:

```
cp -r src dest
```

将目录`src`下的内容复制到`dest`目录下：

```
cp -r src/* dest
```

复制文件，覆盖不询问：

```
cp -nrf a.txt b.txt
```

系统默认给`cp`命令设置了别名`cp -i`，所以，复制时有冲突需要确认，使用如下方式实现默认覆盖：
```
/bin/cp xx yy
```

参考：

- [Linux命令详解之—cp命令](https://www.linuxdaxue.com/linux-command-intro-cp.html)
- [Linux命令命令大全-cp命令](http://man.linuxde.net/cp)
- [Linux 使用 cp 命令强制覆盖功能](https://blog.csdn.net/xinluke/article/details/52229431)

## curl

发出 Get 请求，服务器返回的内容会在命令行输出：

```
curl https://www.example.com
```

- `-o` 将服务器的回应保存成文件，等同于 wget 命令；
- `-O` 将服务器的回应保存成文件，并将 URL 的最后部分当作文件名；
- `-i` 显示 HTTP Response 的头信息，连同网页代码一起；
- `-A` 指定 User-Agent；
- `-H` 添加 HTTP 请求的标头；
- `-d` 发送 POST 请求的数据体；
- `-F` 用来向服务器上传二进制文件；
- `-k` 指定跳过 SSL 检测；
- `-u` 指定服务器认证的用户名和密码；
- `-x` 指定 HTTP 请求的代理；
- `-X` 支持其他动词，比如 POST，发送表单信息，`curl -X POST --data "data=xxx" example.com/form.cgi`
- `-v` 显示一次 HTTP 通信的整个过程，包括端口连接和 HTTP Request 头信息；

Tips:
- FireFox/Chrome的Dev Tools可以直接把请求复制成 curl 的命令，然后可以直接在 shell 里运行
- Postman 也可以把保存的请求复制成 curl 命令，方便在服务器中调试；

参考：
- [阮一峰 - curl 的用法指南](http://www.ruanyifeng.com/blog/2019/09/curl-reference.html)
- [阮一峰 - curl网站开发指南](http://www.ruanyifeng.com/blog/2011/09/curl.html)

## crontab

```
# For details see man 4 crontabs

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name  command to be executed
```

![!\[图片描述\]\[1\]](https://img-blog.csdnimg.cn/20181122165329818.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1OTMyOTA=,size_16,color_FFFFFF,t_70)

在以上各个字段中，还可以使用以下特殊字符：

- 星号（*）：代表所有可能的值，例如month字段如果是星号，则表示在满足其它字段的制约条件后每月都执行该命令操作。
- 逗号（,）：可以用逗号隔开的值指定一个列表范围，例如，“1,2,5,7,8,9”
- 中杠（-）：可以用整数之间的中杠表示一个整数范围，例如“2-6”表示“2,3,4,5,6”

正斜线（/）：可以用正斜线指定时间的间隔频率，例如“0-23/2”表示每两小时执行一次。同时正斜线可以和星号一起使用，例如*/10，如果用在minute字段，表示每十分钟执行一次。

任务执行文件所在位置：
```
/var/spool/cron/crontabs
```

命令参数：
- `-u` user：用来设定某个用户的crontab服务；
file：file是命令文件的名字,表示将file做为crontab的任务列表文件并载入
- `-e`：编辑某个用户的crontab文件内容。如果不指定用户，则表示编辑当前用户的crontab文件。
- `-l`：显示某个用户的crontab文件内容，如果不指定用户，则表示显示当前用户的crontab文件内容。
- `-r`：从/var/spool/cron目录中删除某个用户的crontab文件，如果不指定用户，则默认删除当前用户的crontab文件。
- `-i`：在删除用户的crontab文件时给确认提示

推荐一个有趣的网站，可以查看 crontab 语法含义：

- https://crontab.guru/

每隔2分钟执行`/tmp/test.sh`脚本：
```
crontab -e # 使用crontab -e命令，编辑的是/var/spool/cron下对应用户的 cron 文件
*/2 * * * * /tmp/test.sh
```

第星期六、星期日的时10分重启smb-也就是每周六、周日：
```
10 1 * * 6,0 /etc/init.d/smb restart >>  /tmp/run.log 2>&1
```

实例4：每隔两天的上午8点到11点的第3和第15分钟执行：
```
3,15 8-11 */2  *  * myCommand
```

实例5：清理httpd服务日志超过3天的内容：
```
0 5 * * * /usr/bin/find /var/log/httpd/ -type f -mtime +3 -exec rm -rf {} \;
```

实例6：通过正则清理指定文件夹的内容
```
#update-20181122: clean dir +120 days
0 6 * * * find /data/michael -maxdepth 7 -type d -mtime +120 -regextype posix-egrep -regex '.*/[0-9]{2}/[0-9]{6}$' -exec rm -rf {} \;
```

启动 / 停止 / 重启 crontab

```
$ /etc/init.d/crond start
$ /etc/init.d/crond stop
$ /etc/init.d/crond restart
```

查看日志

```
$ tail -f /var/log/cron
```

参考：

- [如何查看crontab的日志记录](http://blog.51cto.com/461205160/1736383)
- [runoob-Linux Crontab 定时任务](http://www.runoob.com/w3cnote/linux-crontab-tasks.html)
- [LinuxTools-crontab 定时任务](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/crontab.html) 推荐
- [每天一个linux命令（50）：crontab命令](http://www.cnblogs.com/peida/archive/2013/01/08/2850483.html)

## date

选项：
不加: 显示当前的时间.
-d <字符串>：显示字符串所指的日期与时间。字符串前后必须加上双引号；
-s <字符串>：根据字符串来设置日期与时间。字符串前后必须加上双引号；
-u：显示GMT；
--help：在线帮助；
--version：显示版本信息。

参数 <+时间日期格式>：指定显示时使用的日期时间格式。
就是格式化字符串处理.当需要用到空格时要使用双引号,如`"+%Y-%m-%d %H:%M:%S"`.

一般,`%Y %m %d %H %M %S` 是最基本的. 使用星期月份时也会用到`%a %b`

示例
```
VERSION=$(date +%Y%m%d%H%M%S) #20180410192702 #date后面有空格
time=$(date "+%Y-%m-%d %H:%M:%S") #时间格式中有空格，需要加引号
» date "+%Y-%m-%d %T %a %A"
2018-06-04 11:31:25 Mon Monday
```

参考：
- [Shell中date命令用法](http://gohom.win/2015/06/07/bash-date-usage/)

## df
通过`df`命令很容易发现那个磁盘的存储空间快没了。查看挂载状态和硬盘使用量信息：
```
df -hT
```

## dig

dig 和 nslookup 作用有些类似，都是DNS查询工具。dig，其实是一个缩写，即 `Domain Information Groper`。

查看域名的信息。一般一个域名都会绑定到多个 IP 上，ping 命令只能一次看到一个 ip，这个可以看到域名解析的信息。
```
dig baidu.com
```

可能机器上没有这个命令，可以如下[方式](https://unix.stackexchange.com/questions/121874/how-to-install-dig-on-centos)安装：
```
yum install -y bind-utils
```

参考：
- [《dig挖出DNS的秘密》-linux命令五分钟系列之三十四](http://roclinux.cn/?p=2449) Linux 大棚
- [xindoo-csdn-我常用的一些linux命令](https://blog.csdn.net/xindoo/article/details/85193744)

## du
`du`命令可以显示某个特定目录（默认情况下是当前目录）的磁盘使用情况。这一方法可以判断系统上某个目录下是不是有超大的文件。

查看当前文件夹的文件大小：
```
du -sh *
du -sh /usr/* | sort -rn # 按M大小排序
```

## env
查看环境变量值，例：
查看带有SVN的环境变量值：
```
env|grep SVN
```

## find

实例：定时清理httpd服务日志超过3天的内容：

```
0 5 * * * /usr/bin/find /var/log/httpd/ -type f -mtime +3 -exec rm -rf {} \;
```

实例： 通过正则清理指定文件夹的内容
```
#update-20181122: clean dir +120 days
0 6 * * * find /data/michael -maxdepth 7 -type d -mtime +120 -regextype posix-egrep -regex '.*/[0-9]{2}/[0-9]{6}$' -exec rm -rf {} \;
```

实例：搜索/etc目录下第一层的conf配置文件，文件夹下层的不需要
```
find /etc/ -maxdepth 1 -name "*.conf" # 最好加上引号
```

实例：只搜索当前目录，但是不包括.git目录，统计目录数
```
find . -maxdepth 1 -mindepth 1 -type d | grep -v .git |wc -l
```

实例：仅列出目录
```
find . -type d -maxdepth 1
```

参考：
- [Linux中find常见用法示例
](https://www.cnblogs.com/wanqieddy/archive/2011/06/09/2076785.html)
- [FIND中正则表达式的使用](http://smilejay.com/2011/12/find_regular_expression/)
-[Linux中find命令基本使用方法](https://blog.csdn.net/weixin_43314056/article/details/84074998)

## fdisk
`fdisk -l`看到目前系统中所有分区的信息
https://blog.csdn.net/cc_net/article/details/2894510

## free
free命令可以显示Linux系统中空闲的、已用的物理内存及swap内存,及被内核使用的buffer。在Linux系统监控的工具中，free命令是最经常使用的命令之一。

```
free -h -s 2 -t
```
- `-h` 单位会更人性化
- `-s` 每2秒，显示内存使用信息
- `-t` 显示内存总和

```
# 结果
              total        used        free      shared  buff/cache   available
Mem:           7.6G        935M        6.1G        9.7M        631M        6.4G
Swap:          7.5G          0B        7.5G
Total:          15G        935M         13G
```

- total:总计物理内存的大小。
- used:已使用多大。
- free:可用有多少。
- Shared:多个进程共享的内存总额。
- Buffers/cached:磁盘缓存的大小
- 交换分区SWAP，也就是我们通常所说的虚拟内存

从应用程序角度来看，对于应用程序来说，buffers/cached 是等于可用的，因为buffer/cached 是为了提高文件读取的性能，当应用程序需在用到内存的时候，buffer/cached 会很快地被回收。

所以从应用程序的角度来说 `可用内存=系统free memory+buffers+cached`

我们看linux,只要不用swap的交换空间,就不用担心自己的内存太少.如果常常swap用很多,可能你就要考虑加物理内存了.这也是linux看内存是否够用的标准.

+buffers/cache,即对应用程序来说free的内存太少了，也是该考虑优化程序或加内存了

参考：

- [每天一个linux命令（45）：free 命令](http://www.cnblogs.com/peida/archive/2012/12/25/2831814.html)

## fuser

fuser通常被用在诊断系统的“resource busy”问题，通常是在你希望umount指定的挂载点得时候遇到。 如果你希望kill所有正在使用某一指定的file, file system or sockets的进程的时候，你可以使用 `-k option`

```
fuser –k –i /path/to/your/filename # 加上-i 表示杀死之前，需确认
```

- [fuser命令](http://man.linuxde.net/fuser)
- [fuser 命令小结](https://www.cnblogs.com/yuboyue/archive/2011/07/18/2109838.html)

## groups

```
whoami # 查看用户名
groups # 查看当前用户所属组
```

## grep
语法：
```
grep [options] pattern [file]
```

```shell
# 递归、显示行号、忽略大小写、显示搜到的匹配内容上下2行 搜索范围是当前目录下
grep -rni 'github.com'  -C 2 .
```

```shell
grep 'shopbase' /home/admin -r -n --include *.{vm,java} #指定文件后缀
grep 'shopbase' /home/admin -r -n --exclude *.{vm,java} #反匹配
```

参考：

- [man-grep](https://man.cx/grep)
- [Linux查找文件内容（grep）](https://www.eguidedog.net/linux-tutorial/05-grep.php)
- [每天一个linux命令（39）：grep 命令](http://www.cnblogs.com/peida/archive/2012/12/17/2821195.html)
- [我的java问题排查工具单](https://yq.aliyun.com/articles/69520?utm_content=m_10360)

## gzip

gzip是GNU项目的产物。这个软件下买呢含有下面的工具：

- gzip ：用来压缩文件
- gzcat：用来查看压缩过的文本文件的内容
- gunzip:用来解压文件。

```
gzip xxx
gzip -l <filename> # list compressed file contents
```

## head
显示前n行内容：

```
head -n
```

https://www.linuxdaxue.com/linux-command-intro-head.html

## less

在`more`的时候，我们并没有办法向前面翻，只能往后面看，但若使用了`less`时，就可以使用 `[pageup] [pagedown]` 等按键的功能来往前往后翻看文件。

## locate

```
locate  GPG-KEY
# find /etc -name '*GPG-KEY*' 等同
```

可能系统没有自带`locate`命令，可以使用`yum install mlocate -y`安装，安装结束执行`updatedb`命令。

## ls

仅显示目录：

```
ll -d
```

ls命令显示文件大小，会根据文件大小自己决定单位，M或者Kb或者G

```
ll -h
```

## mount

`mount`可以显示全部挂载情况。

将分区挂载到目录：

```
mount /dev/xvde /data
```

- [Linux mount命令](http://www.runoob.com/linux/linux-comm-mount.html)

## mkdir

`mkdir sysadmin/admim_{1,2,3,4,5}`

参考：
- [在Linux中用chattr和lsattr命令管理文件和目录属性](https://blog.csdn.net/robertsong2004/article/details/46437681)

## netstat
检查端口占用

```
netstat -anp|grep 80
```

- [linux(redhat,centos)释放被占用端口](https://blog.csdn.net/cuiyong_xu/article/details/42556925)

## ping

`ping baidu.com`

- [生活在宁静的角落——PING命令的各类反馈信息](http://www.on0926.com/?p=901)

## rm
只删除当前文件夹下的隐藏文件和隐藏文件夹：
```
rm -rf .*
```
https://blog.csdn.net/ficksong/article/details/52447729

## rpm
我的系统中安装了那些rpm软件包
```
rpm -qa
```

如果要查找所有安装过的包含某个字符串sql的软件包
```
rpm -qa | grep sql
```
一个rpm包中的文件安装到那里去了？
```
rpm -ql 包名
```
软件包的卸载
```
rpm -e
```

参考：http://man.linuxde.net/rpm

## rpm2cpio

RPM 包解压缩：
```
# 注意，要加上 cpio -div，否则终端会打印多余的内容出来
rpm2cpio xxxx.rpm | cpio -div
```

你的 Linux 下可能没有rpm2cpio这个命令，用过简单指令安装即可：

```
sudo apt-get install  rpm2cpio
sudo yum install rpm2cpio
```

## sed

eg1：截取日志中的两行之间的内容，同时去掉匹配的首尾行：
```
cat mock.log |sed -n '/tee/,/find/p' mock.log|sed -n '1!p'|sed -n '$!p'|awk '{print $2,$3}'
```

eg2:
```
nl passwd|sed "1d;10d" #删除第1行，第10行
```

eg3:
```
sed -i -e "1i%define upstream_version $UPSTREAMVERSION\\" *.spec #第一行插入
sed -i -e "s/UPSTREAMVERSION/$UPSTREAMVERSION/g" *.spec #替换
```

参考：
- [SED 简明教程](https://coolshell.cn/articles/9104.html)
- [sed、awk——运维必须掌握的两个工具](https://my.oschina.net/u/3422445/blog/1816311)
- [看例子学sed](http://qinghua.github.io/sed/)
- [三十分钟学会SED](https://github.com/mylxsw/growing-up/blob/master/doc/%E4%B8%89%E5%8D%81%E5%88%86%E9%92%9F%E5%AD%A6%E4%BC%9ASED.md)
- [sed命令筛选指定字符串之间的行](https://www.oschina.net/question/1375377_127871)

## SELinux

```shell
sestatus [-v] # 查看selinux开启状态
getenforce # 查看当前selinux的状态
```

selinux开启常常影响其他一些服务，比如httpd等，所以，运维往往一般拿到机器就会默认将其关闭。

```shell
setenforce 1 # 设置SELinux 成为enforcing模式
setenforce 0 # 设置SELinux 成为permissive模式 不重启关闭selinux的解决办法
```

开机重启后，上面利用`setenforce`方式修改的值会失效，所以，开机重启也有效的话，需要修改如下文件：

```
/etc/selinux/config # 文件的软链接是/etc/sysconfig/selinux 是
```

- [wiki-SELinux](https://wiki.centos.org/zh/HowTos/SELinux)
- [Linux 下为何要关闭 SELinux？](https://www.zhihu.com/question/20559538)
- [如何开启或关闭SELinux](https://www.cnblogs.com/lidp/archive/2009/05/12/1697984.html)
- [SELinux开启与关闭各参数说明](http://www.upvup.com/html/SELinux/2014-06-11/4.html)

## sort

对之前提到的密码文件`/etc/passwd`根据用户ID进行数值排序。`-k`和`-t`参数在对安字段分割的数据进行排序时非常有用。

```
sort -t ":" -k 3 -n /etc/passwd
```

## systemctl

| 任务                 | 旧指令                       | 新指令                             |
| -------------------- | ---------------------------- | ---------------------------------- |
| 使某服务自动启动     | chkconfig –level 3 httpd on  | systemctl enable httpd.service     |
| 使某服务不自动启动   | chkconfig –level 3 httpd off | systemctl disable httpd.service    |
| 检查服务状态         | service httpd status         | systemctl status httpd.service     |
| 显示所有已启动的服务 | chkconfig –list              | systemctl list-units –type=service |
| 启动某服务           | service httpd start          | systemctl start httpd.service      |
| 停止某服务           | service httpd stop           | systemctl stop httpd.service       |
| 重启某服务           | service httpd restart        | systemctl restart httpd.service    |
- [Linux 设置程序开机自启动 （命令systemctl 和 chkconfig用法区别比较）](https://blog.csdn.net/kenhins/article/details/74518978)

## sha256sum
生成文件对应的sha256值：

```
sha256sum FusionSphere_Upgrade_6.2.50.4001.tar.gz > a.sha256sum # 校验
sha256sum -c <(grep FusionSphere_Upgrade_6.2.50.4001.tar.gz a.sha256sum) # 校验
```

参考：
- [linux下生成sha256校验文件、使用sha256校验某个文件](https://blog.csdn.net/qq_28082757/article/details/78541581)

## tar

目前Unix和Linux上最广泛使用的归档工具是 tar 命令。

```
tar function [options] object1 object2 ……
```

首先，创建一个归档文件：

```
tar -cvf test.tar test/ test2/
```

创建了名为test.tar归档文件，含有 test 和 test2 目录内容。

接着，列出 tar 文件 test.tar 内容（但并不提取文件）：

```
tar -tf test.tar
```

最后用命令提取文件：

```
tar -xvzf test.tar
```

**tar命令是给整个目录创建归档文件的简便方法**

> 窍门：下载开源软件之后，经常会看到文件名以.tgz结尾。这些事gzip压缩过的tar文件，可以用tar -zxvf filename.tgz来解压

## tee

tee命令用于将数据重定向到文件，另一方面还可以提供一份重定向数据的副本作为后续命令的stdin。简单的说就是把数据重定向到给定文件和屏幕上。

eg1 在终端打印stdout同时重定向到文件中：

```
ls | tee out.txt | cat -n
```

eg2 创建daemon.json文件，EOF之间内容作为stdin：

```
tee /etc/docker/daemon.json << EOF
{
    "insecure-registries" : [ "", ""]
}
EOF
```

`<< EOF …… EOF`的作用是在命令执行过程中用户自定义输入，它类似于起到一个临时文件的作用，只是比使用文件更方便灵活。

EOF妙用：

它的作用就是将两个` delimiter `之间的内容(Here Document Content 部分) 传递给`cmd` 作为输入参数。

```
cmd << delimiter
  Here Document Content
delimiter
```

```
[root@ecs-6b86 tmp]# cat << EOF >tt.sh
123123123
345345
asdfasds
EOF
```

自定义EOF，比如自定义为michael
[root@slave-server opt]# cat << michael > haha.txt
> ggggggg
> 4444444
> 6666666
> michael

`<<` 变为 `<<-`。 使用 `<<-` 的唯一变化就是Here Document 的内容部分每行前面的 `tab` (制表符)将会被删除掉，这种用法是为了编写Here Document的时候可以将内容部分进行缩进，方便阅读代码。

有时脚本内容里变量不想被系统环境变量替换掉，可以通过在起始的 delimiter的前后添加 " 来实现

参考：

- [linux shell 的here document 用法 (cat << EOF)](https://my.oschina.net/u/1032146/blog/146941)
- [EOF是什么？](http://www.ruanyifeng.com/blog/2011/11/eof.html)
- [linux shell脚本EOF妙用](https://blog.csdn.net/zongshi1992/article/details/71693045)

## tree

```
tree -FCL 2 FusionUpgrade
```

- [linux tree命令--显示目录的树形结构](https://blog.csdn.net/u011729865/article/details/53368446#tree-l-2)

## time

```
time nslookup michael.com
nslookup: can't resolve '(null)': Name does not resolve

Name:      micahel.com
Address 1: 10.248.250.158
real    0m 5.00s
user    0m 0.00s
sys     0m 0.00s
```

- [time](http://man.linuxde.net/time)

## test

- 判断字符串是否为空，可以通过`help test`查看

```
      -z STRING      True if string is empty.

      -n STRING
         STRING      True if string is not empty.
```

示例：

```
#!/bin/sh

STRING=""
# -z
if [ -z "$STRING" ]; then
    echo "STRING is empty"
fi

if [ -n "$STRING" ]; then
    echo "STRING is not empty"
fi

# STRING is empty
```

- [linux shell 中判断字符串为空的正确方法](https://www.cnblogs.com/cute/archive/2011/08/26/2154137.html#commentform)：有趣的示例，强调了需要加引号的重要性
- [Linux shell 编程 字符串null值 的 条件判断?](https://www.zhihu.com/question/22539151)：解释了，为何加引号，bash的内建命令test在只有一个参数的情况下，只要参数不为空就返回真

## tcpdump

首先，先用 `tcpdump -D` 命令列出可以抓包的网络接口：

```shell
$ tcpdump -D
1.virbr0
2.docker0
3.bluetooth0 (Bluetooth adapter number 0)
4.nflog (Linux netfilter log (NFLOG) interface)
5.nfqueue (Linux netfilter queue (NFQUEUE) interface)
6.usbmon1 (USB bus number 1)
7.usbmon2 (USB bus number 2)
8.wlp3s0
9.enp5s0
10.any (Pseudo-device that captures on all interfaces)
11.lo [Loopback]
```

其中， `lo` 就是 `localhost` 。其中特殊接口 any 可用于抓取所有活动的网络接口的数据包。

```
$ sudo tcpdump -i any -c5 -nn icmp port 80 -A -w webserver.pcap

tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on any, link-type LINUX_SLL (Linux cooked), capture size 262144 bytes
22:10:51.809330 IP 192.168.3.43.22 > 192.168.3.66.50051: Flags [P.], seq 2040167007:2040167203, ack 59350146, win 1432, options [nop,nop,TS val 89942170 ecr 111122685], length 196
22:10:51.812472 IP 192.168.3.43.22 > 192.168.3.66.50051: Flags [P.], seq 196:416, ack 1, win 1432, options [nop,nop,TS val 89942173 ecr 111122685], length 220
22:10:51.833093 IP 192.168.3.66.50051 > 192.168.3.43.22: Flags [.], ack 0, win 23490, options [nop,nop,TS val 111123121 ecr 89942122], length 0
22:10:51.833193 IP 192.168.3.43.22 > 192.168.3.66.50051: Flags [P.], seq 416:612, ack 1, win 1432, options [nop,nop,TS val 89942194 ecr 111123121], length 196
22:10:51.835541 IP 192.168.3.66.50051 > 192.168.3.43.22: Flags [.], ack 196, win 23487, options [nop,nop,TS val 111123121 ecr 89942170], length 0
5 packets captured
7 packets received by filter
0 packets dropped by kernel
```

- `-c` 选项可以用于限制 tcpdump 抓包的数量
- 用 `-n` 选项显示 IP 地址，`-nn` 选项显示端口号
- `icmp` 这里用作过滤条件，只要抓取 `ICMP` 报文
- `port` 指定端口号，根据端口号来筛选数据包
- tcpdump 提供了两个选项可以查看数据包内容，`-X` 以十六进制打印出数据报文内容，`-A` 打印数据报文的 ASCII 值
- 使用 `-w` 选项来保存数据包而不是在屏幕上显示出抓取的数据包

tcpdump 将数据包保存在二进制文件中，所以不能简单的用文本编辑器去打开它。使用 `-r` 选项参数来阅读该文件中的报文内容：
```
tcpdump -nn -r webserver.pcap
```

用 host 参数只抓取和特定主机相关的数据包：
```
sudo tcpdump -i any -c5 -nn host 54.204.39.132
```

可以使用括号来创建更为复杂的过滤规则，但在 shell 中请用引号包含你的过滤规则以防止被识别为 shell 表达式：
```
$ sudo tcpdump -i any -c5 -nn "port 80 and (src 192.168.122.98 or src 54.204.39.132)"
```

- `src` 抓取源 IP 地址
- 使用 `dst` 就是按目的 IP/主机名来筛选数据包
- 使用 `and` 以及 `or` 逻辑操作符来创建过滤规则

参考：

- [伯乐在线-在 Linux 命令行中使用 tcpdump 抓包](http://blog.jobbole.com/114505/#comment-162685)
- [运维之路-NAS存储抓包分析](http://www.361way.com/nas-netcap-analysis/5882.html)
- [Huang-tcpdump 常用操作](https://mozillazg.com/2018/01/tcpdump-common-useful-examples-cookbook.html)
- [Tcpdump入门教程示例](https://liyang85.com/tcpdump-tutorial-with-examples)
- [9个tcpdump使用实例](http://www.cnblogs.com/bangerlee/articles/2545612.html)

## top

top 命令是 Linux 下常用的性能分析工具，能够实时显示系统中各个进程的资源占用状况。

- `load average: 0.02, 0.04, 0.00`： 系统1分钟、5分钟、15分钟的CPU负载信息

top 默认按 cpu 占用排序，这也是可以修改的，按 F(大写)即可选择相应排序，之后任意键退出即可。

进程信息：

- PID：进程的ID
- USER：进程所有者
- PR：进程的优先级别，越小越优先被执行
- NInice：值
- VIRT：进程占用的虚拟内存
- RES：进程占用的物理内存
- SHR：进程使用的共享内存
- S：进程的状态。S表示休眠，R表示正在运行，Z表示僵死状态，N表示该进程优先值为负数
- %CPU：进程占用CPU的使用率
- %MEM：进程使用的物理内存和总内存的百分比
- TIME+：该进程启动后占用的总的CPU时间，即占用CPU使用时间的累加值。
- COMMAND：进程启动命令名称

交互操作：

- c:显示进程命令的全路径与参数
- f:可以指定top显示的内容，如ppid、swap等都可以选择显示
- k:输入k之后可以kill掉指定的进程
- A:分类显示各种系统资源高的进程。可用于快速识别系统上的性能要求极高的任务，推荐使用
- h:获取top的命令帮助
- H:显示线程，默认只显示进程
- W[大写]:将当前设置写入~/.toprc文件中。这是写top配置文件的推荐方法

参考：

- [Top实践小技巧](http://kumu-linux.github.io/blog/2013/06/07/top-hacks/)

## useradd/groupadd

Linux 系统的用户、群组添加、管理

```shell
# 添加一个用户为 michael，属于基本组(主组) root。如果不加 -g，则默认会属于和用户名同名的主组
useradd -g root michael
# 修改密码
passwd michael
# 新增一个 michael 的组
groupadd michael
# 修改用户名的主组为 michael
usermod -g michael michael
# 删除用户 michael
userdel michael
```

参考：
- [博客园-Linux用户和组管理，添加修改用户，添加修改组，加入组，移除组](https://www.cnblogs.com/woshimrf/p/linux-user-group-command.html#commentform)

## unzip
```
unzip -l demo1-0.1-py2.7.egg
```
```
unzip -o -d /home/sunny myfile.zip
```
把myfile.zip文件解压到 `/home/sunny/`
`-o`:不提示的情况下覆盖文件；
`-d`:-d /home/sunny 指明将文件解压缩到/home/sunny目录下；

参考：
- [Linux下的压缩（zip）解压(unzip)缩命令](https://blog.csdn.net/shenyunsese/article/details/17556089)
- [unzip命令](http://man.linuxde.net/unzip)

## wc
```
wc [-clw][--help][--version][文件...]
```
参数：
```
-c或--bytes或--chars 只显示Bytes数。
-l或--lines 只显示行数。
-w或--words 只显示字数。
--help 在线帮助。
--version 显示版本信息。
```

## who
who  //显示当前登录系统的用户
显示标题栏
```
# who -H
```

只显示当前用户
```
# who -m -H
```

## wget

将远程目录下的全部内容下载到`save目录下`。`-nd`参数表示，如果远程目录下也有子目录，会将子目录中的文件下载下来而不创建多余目录。

```
wget -r -np -nd -R "index.html*" -P test http://xxx/FusionUpgrade/master/euler/20181101130551/ # 注意，URL末尾需要有/，否则会递归下载的
```

- -r：递归下载，下载指定网页某一目录下（包括子目录）的所有文件
- -nd:–-no-directories 不创建目录
- -np：–-no-parent 不要追溯到父目录
- P:指定下载下来的存放目录，没有会自动创建
- -nH:–-no-host-directories 不创建主机目录

示例2：

将远程文件夹原封不动下载下来，并且下载下来的本地路径也是远程目录，而不会创建多级目录。`-nH`表示不会创建
`xxx.com`目录，`--cut-dirs`将其余多余层级目录不下载，实现效果下载到本地就只是`DLRN_RPMS`目录。

```
wget -r -p -k -np -nH --cut-dirs=4 http://xxx.com/cps/FusionNetwork-for-fc/master/suse/DLRN_RPMS/
```

参考：

- [wget 文件下载](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/wget.html)
- [wget](https://github.com/jaywcjlove/linux-command/blob/master/command/wget.md)
- [wget递归下载文件](https://www.cnblogs.com/include/archive/2011/10/17/2215607.html)

## watch

```
watch -d 'ls -l|grep scf'       # 监测当前目录中 scf' 的文件的变化
watch -n 10 'cat /proc/loadavg' # 10秒一次输出系统的平均负载
watch -n 1 -d netstat -ant       # 命令：每隔一秒高亮显示网络链接数的变化情况
```

参考：

- [watch-命令](https://wangchujiang.com/linux-command/c/watch.html)

## systemd service服务

- [Systemd 入门教程：命令篇](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)

## xargs

为了快速修改后缀名字

源文件夹下：

```shell
CentOS-base.repo.repo.bak
epel.repo.repo.bak
```

方法一：

```shell
ls *.bak|awk -F. '{print $1}'|xargs -t -i mv {}.repo.repo.bak {}.repo
```

参考：
- [阮一峰——xargs 命令教程](http://www.ruanyifeng.com/blog/2019/08/xargs-tutorial.html)
