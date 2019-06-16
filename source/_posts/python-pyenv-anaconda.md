---
title: Python多版本共存工具-Pyenv及Anaconda科学计算环境的配置
date: 2015-11-26 16:22:29
tags: [Anaconda, Python]
categories: Python
thumbnail: https://www.enthought.com/images/pandas_logo.png
---

为了安装Anaconda科学计算环境，控制好Python版本，今天上午总算折腾好了。

+ 学习python有时希望在python2.7环境下，有时希望在python3.4环境下，该怎么办呢？
+ Anconda的包也不知道适合在什么环境下工作？

解决多python环境下，python版本切换的工具--pyenv应运而生。同时，另外一个工具virtualenv则提供了一种功能， 就是将一个目录建立为一个虚拟的python环境， 这样的话， 用户可以建立多个虚拟环境， 每个环境里面的python版本可以是不同的， 也可以是相同的， 而且环境之间相互独立。
下面简要介绍一下安装的过程：
<!-- more -->
# Unix系统下pyenv安装与使用:

## 安装pyenv

```
$ git clone https://github.com/yyuu/pyenv.git ~/.pyenv     #使用 git 把 pyenv 下载到家目录
$ echo 'export PYENV_ROOT="$HOME/.pyenv"' >> ~/.bashrc     #然后需要修改环境变量，使用 Bash Shell 的输入

$ echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> ~/.bashrc

$ echo 'eval "$(pyenv init -)"' >> ~/.bashrc     #最后添加 pyenv init

$ exec $SHELL -l     #输入命令重启 Shell,然后就可以重启pyenv
```

## 查看pyenv可安装的版本列表

```
$ pyenv install --list
```
該命令将列出pyenv可以安装的列表，单单列举几个咱们关心的python和anaconda把：

```
2.7.8   # Python 2最新版本

3.4.1   # Python 3最新版本

anaconda-2.4.0  # 支持Python 2.6和2.7

anaconda3-2.0.1 # 支持Python 3.3和3.4

```
## 安装指定的python版本。

```
$ pyenv install 3.4.1

```
该命令会从github上下载python的源代码安装，但是，我这里主要是用来进行科学计算的环境安装，因此，最后选择安装
anaconda-2.4.0（python2.7环境） 和 anaconda3-2.4.0（python3.4环境）。可以选择都安装，之后可以使用pyenv进行版本的切换。
注：
+ 这里利用pyenv命令安装的python版本都安装在~.pyenv/versions文件夹下，当然，如果你从图形界面进入Ubuntu的用户主目录下可能看不见.pyenv文件夹，这时候，你可以使用**`ls -a`**看到隐藏的文件夹。
+ 使用`pip`安装的包完成之后，可能需要对数据库进行更新:
```
pyenv rehash
```


## 卸载指定的python版本
```
pyenv uninstall x.x.x
```

## python版本查看

查看当前已经安装了的python版本：
```
pyenv versions
```
输出结果如下：
```
*system (set by /home/michael/.pyenv/version)
2.7.1
3.4.1
anaconda-2.4.0
```
system是指系统的python版本；*表示当前环境所处于的python版本

## python版本切换
全局版本切换：
```
pyenv global anaconda-2.4.0
```
全局切换为anaconda科学计算环境，因为，我现在也不做其他python开发，所以，无需再安装其他环境了。
查看现在的python版本：

```
michael@michael-ThinkCentre-XXXX:~$ pyenv versions
system
2.7.1
3.4.1
* anaconda-2.4.0 (set by /home/michael/.pyenv/version)
```
有全局版本切换，当然也会有局部环境的切换：
在test文件夹下希望切换到python3.4.1:
```
pyenv local python3.4.1

```

# Anaconda科学计算包的使用：

## 使用`conda list`查看anaconda安装自带的包：

```
michael@michael-ThinkCentre-XXXX:~/test$ conda list
# packages in environment at /home/michael/.pyenv/versions/anaconda-2.4.0:
#
abstract-rendering 0.5.1 np110py27_0
alabaster 0.7.6 py27_0
anaconda 2.4.0 np110py27_0
anaconda-client 1.1.0 py27_0
argcomplete 1.0.0 py27_1
astropy 1.0.5 np110py27_1
babel 2.1.1 py27_0
backports.ssl-match-hostname 3.4.0.2 <pip>

```

注：
+ 使用`conda list`命令的环境时python版本切换到anaconda版本下，不然，这个命令无法来查询。
+ 看上面列表就能知道，anaconda的环境下，也是能使用系统`pip`命令安装的包的！

## 给anaconda安装包

```
conda install ××××

```

## 如果需要指定包的版本。
```
[package-name]=x.x   #指定包的版本
```
## 多个Python版本并存，尤其是2.x和3.x的并存。

这个通过virtualenv可以做到。Anaconda也正是通过其实现的。下面用conda创建一个名叫python2的版本为python2.7的环境。

```
conda create -n python2 python=2.7

```
这样就会在Anaconda安装目录下的envs目录下创建python2这个目录。
向其中安装扩展可以：

直接用conda install并用-n指明安装到的环境，这里自然就是python2。
像virtualenv那样，先activate，然后在虚拟环境中安装。
注：
+ 关于`virtualenv`的使用，还需了解，可以参考python生态
+ 下面的操作，貌似会安装很多包，谨慎使用。

```
conda create -n py34 python=3.4 anaconda

```

# 利用Pycharm的版本切换功能~推荐！很方便！
可以选择安装anaconda 不同的版本，然后按照下面操作,切换版本。Windows/Linux下都有[Pycharm][1]。

> 打开pycharm，打开preference --> project --> project interpreter-->

# Windows系统切换
首先当然是安装你需要的两个不同版本的python，我安装的是2.7和3.4的，两个版本安装顺序无所谓，但是后面安装的会变成默认的（因为我是后安装的python 3.4，它就变成了默认的python）。

然后去python27 文件夹下面把python.exe改名python2.exe，然后就可以在命令行下通过python或者py来调用3.x，python2来调用2.x。

另外pip的话直接使用 pip2 或者 pip3 就可以了。



# 参考：
## pyenv

1. [Python多版本共存之pyenv](http://seisman.info/python-pyenv.html)
2. [yyuu/pyenv-github](https://github.com/yyuu/pyenv#installation)
3. [用pyenv 和 virtualenv 搭建单机多版本python 虚拟开发环境](http://www.cnblogs.com/npumenglei/p/3719412.html)
4. [lixm/pybooklet-github](https://github.com/lixm/pybooklet/blob/master/install.md)
5. [使用 pyenv 和 Miniconda 管理 Python 科学计算环境](http://huangziwei.com/tech/setting-up-scientific-python-environment-in-os-x-10-10-using-miniconda/)
6. [Python 2.X 3.X 多版本共存](http://zhangnew.com/2015-06-09-python-2-3-exist.html)
7. [一篇文章入门Python生态系统](http://codingpy.com/article/python-ecosystem-introduction/)

## Anaconda
1. [DOWNLOAD ANACONDA NOW](https://www.continuum.io/downloads)
2. [P]ython科学计算利器——Anaconda](http://www.cnblogs.com/CodeCabin/p/3961118.html)
3. [Python科学计算发行版—Anaconda](http://www.cnblogs.com/super-d2/p/4725818.html)

## Virtualenv(待续)
1. https://virtualenv.pypa.io/en/latest/installation.html

----

