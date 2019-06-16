---
title: Windows下利用批命令修改IP设置
tags: [Tools, 小程序, batch]
toc: true
date: 2016-11-14 16:58:35
categories: Tools
thumbnail: https://cdn.lynda.com/course/435539/435539-636274410487252173-16x9.jpg
---

因为在实验室需要将网络设置为自动获取IP地址和DNS服务器地址，然而，在宿舍想要登录成功6维BT站，却需要配置静态IP地址。每次切换IP需要输入一串参数，打开网络适配器去设置，很麻烦，下午就花时间解决了一下。

<!-- more -->

## 设置静态IP/DNS地址

```shell
netsh int ipv4 set address name="以太网" source=static address=222.26.218.27 mask=255.255.255.40 gateway=222.26.218.254
netsh int ipv4 set dns name="以太网" source=static address=202.118.66.66 register=primary validate=no
```

这里需要注意的是，`name`这个值，是你电脑网络连接的名称，需要到网络适配器中去查看，示意图如下：

![](http://ww4.sinaimg.cn/large/6d9475f6jw1f9rr07pdt5j20cr07gmyi.jpg)

大部分`name`值都是"本地连接"，需要根据你自己的配置修改名称。

- `ipv4 address`:IP地址
- `mask`:子网掩码
- `gateway`:默认网关
- `dns address`:首选DNS服务器

## 设置动态IP/DNS地址

```shell
netsh interface ipv4 set address name="以太网" source=dhcp
netsh interface ipv4 set dnsservers name="以太网" source=dhcp
```

## 命令升级版

将设置静态IP和动态IP集成到一个`bat`文件中，方便执行。

**注意：执行时，需要管理员权限。**

```shell
@echo off
title 一件切换IP地址--Michael翔
color 0a
:begin
echo 一键切换IP地址
echo 1.切换成静态IP（宿舍）
echo 2.切换成动态IP
echo.
@rem echo. 紧跟着一点，效果是空了一行

set /p op=Type option
if "%op%"=="1" goto op1
if "%op%"=="2" goto op2


:op1
echo "你选择了选项1"
netsh int ipv4 set address name="以太网" source=static address=222.26.218.16 mask=255.255.255.0 gateway=222.26.218.254
netsh int ipv4 set dns name="以太网" source=static address=202.118.66.6 register=primary validate=no
goto exit
@rem 这里的go to begin不能少，不然，执行选项1之后，会继续向下执行

:op2
echo "你选择了选项2"
netsh interface ipv4 set address name="以太网" source=dhcp
netsh interface ipv4 set dnsservers name="以太网" source=dhcp
goto exit

:Exit
pasue
```

注解：
在 SET 命令中添加了两个新命令行开关:

```shell
SET /A expression
SET /P variable=[promptString]
```

- /A 命令行开关指定等号右边的字符串为被评估的数字表达式。
- /P 命令行开关允许将变量数值设成用户输入的一行输入。读取输入

行之前，显示指定的 promptString。promptString (提示词)可以是空的

## 命令简介

- echo+message
如果，要在执行`bat`命令时，让`cmd`窗口不一闪而过，在文件末尾添加`pause`就可以了！
例如：`echo “hello michael翔”`

- @echo off
这个效果是不显示后面的执行命令，关闭回显功能；
- rem
注释命令，类似于在C语言中的/*--------*/，它并不会被执行，只是起一个注释的作用
例如：`rem 这里是注释哈！`不会执行，但是，会显示此行，如果要关闭回显，加符号`@`；
- pause
暂停命令。
- goto
跳转命令。程序指针跳转到指定的标签，从标签后的第一条命令开始继续执行批处理程序。
例如：
```
:1
start
goto 1
```
这里就会循环执行，打卡很多窗口。
- set
显示、设置或删除变量
例如：

```shell
set a=2
set b=a
echo %a%
echo %b%
pasue
```

这里是设置`b`为字符串`a`，如果要将`a`值赋值给`b`，应该`b=%a%`。
`set a=`删除变量

## 符号简介

- @
回显关闭，表示不显示`@`后面的命令。

## 命令释义

### 文件夹管理

- cd 显示当前目录名或改变当前目录。
- md 创建目录。
- rd 删除一个目录。
- dir 显示目录中的文件和子目录列表。
- tree 以图形显示驱动器或路径的文件夹结构。
- path 为可执行文件显示或设置一个搜索路径。
- copy 复制文件和目录树。

### 文件管理

- type 显示文本文件的内容。
- copy 将一份或多份文件复制到另一个位置。
- del 删除一个或数个文件。
- move 移动文件并重命名文件和目录。（Windows XP Home Edition中没有)
- ren重命名文件。
- replace 替换文件。
- attrib 显示或更改文件属性。
- find 搜索字符串。
- fc 比较两个文件或两个文件集并显示它们之间的不同

## 参考

- [百科-批处理](http://baike.baidu.com/view/80110.htm)
- [How to script a change to a static IP address or DHCP IP address in Windows using a batch file](https://mikebeach.org/2012/05/02/how-to-script-a-change-to-a-static-ip-address-or-dhcp-ip-address-in-windows/)
- [批处理常用命令总结和WindowsXP下运行命令使用大全及优化](http://blog.csdn.net/z22050519/article/details/17019137)
- [BAT 批处理脚本 教程](http://www.cnblogs.com/glaivelee/archive/2009/10/07/1578737.html)
- [Multiple choices menu on batch file?](http://stackoverflow.com/questions/14529246/multiple-choices-menu-on-batch-file)
- [知道-批处理中set /a和set /p有什么区别，一般怎么用？谢谢回答！](https://zhidao.baidu.com/question/111737455.html?qbl=relate_question_2)
- [贴吧-简述一下怎么自己写批处理吧](http://tieba.baidu.com/p/2308317172)


<script src='//w.segmentfault.com/card/1030000002497141.js?w=0&3rd=1&bg=0&bd=dddddd&cl=333333&btn=009a62&noBtn=1'></script>

----