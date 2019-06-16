---
title: Python 调试模块 ipdb
date: 2018-12-15 12:53:56
tags: [debug, method]
categories: Python
keywords: python, ipdb, debug
---

![](https://ws1.sinaimg.cn/large/6d9475f6ly1fy7ccf1guyj20hs0cgdgh.jpg)

授人以鱼不如授人以渔，掌握调试方法是学习提升的一个必备条件。前几天因为探索了一下 Python 模块引入路径的问题，看到「翔 Core」用 pdb 调试的很溜，因此，今天研究一下 ipdb好啦。

<!-- more -->

## 安装

ipdb  是 pdb 模块的升级版，会启动一个 ipython 的调试窗口。

```python
pip install ipdb
```

## 使用

1. `python -m ipdb demo.py` 直接运行程序，不需要再程序中去插入内容；
2. `import ipdb;ipdb.set_trace()`，这种用法需要在程序内部插入语句，类似插入「断点」的效果；

## 帮助文档

在 ipdb 窗口，输入 h ，会看到帮助文档，`h <command>` 可以查看具体命令的帮助。

```python
Documented commands (type help <topic>):
========================================
EOF    cl         disable  interact  next    psource  rv         unt
a      clear      display  j         p       q        s          until
alias  commands   down     jump      pdef    quit     source     up
args   condition  enable   l         pdoc    r        step       w
b      cont       exit     list      pfile   restart  tbreak     whatis
break  continue   h        ll        pinfo   return   u          where
bt     d          help     longlist  pinfo2  retval   unalias
c      debug      ignore   n         pp      run      undisplay

Miscellaneous help topics:
==========================
exec  pdb
```

## 常用命令

- `a(rgs)`：打印当前函数的参数列表 ★★★
- `b(reak) [ ([filename:]lineno | function) [, condition] ]`：设置断点，可以接行数或者行数名作为参数，比如，`b 8` 就是在第8行打断点，还可以给外部的文件加上断点 `b file_name:line_number`，如果只敲 `b` ，会显示全部断点
- `c(ont(inue))`：继续执行，当遇到断点时才会停止 ★★★
- `cl(ear) filename:lineno` ：可以清除断点，不过不加「断点号」，可以清除所有断点
- `disable bpnumber [bpnumber ...]` ：禁用指定[断点号]的断点
- `enable bpnumber [bpnumber ...]`：启用指定[断点号]的断点
- `Enter`：重复上次命令 ★★★
- `j(ump)`：设置下一步执行的行，这个语句可以让你跳过某些语句的执行，并不是网上一些文章说的`跳到某行`，跳到某行可不是代表会跳过某些语句的，文档最准确
- `ll` or `l`：查看当前将要运行的代码段
- `n(ext)`：让程序运行下一行，如果当前语句有一个函数调用，用 n 是不会进入被调用的函数体中的 ★★★
- `pp expression` or `p expression`：前者以 pprint 方式打印表达式的值，后者 print 方式 ★★★
- `q(uit)`：退出调试，同时，你也可以使用 `Ctrl+D`退出调试
- `r(eturn)`：继续执行，直到当前函数返回 ★★★
- `restart "restart" is an alias for "run".` ：是 `run` 命令的别名，重新启动调试器；
- `run [args...]` ：这里的参数会作为运行脚本的参数，等价于 `python demo.py [args...]`，可以通过 `sys.argv` 查看参数列表，重新运行时，断点等信息会保留的
- `s(tep)`：和 n 相似，但是如果当前有一个函数调用，s 会进入被调用的函数体中 ★★★
- `w(here)`：打印一个堆栈跟踪，
- `!` ：感叹号后面跟着语句，可以直接改变某个变量的值

## 示例

### 断点示例

下面是一个关于断点的示例：
执行 `b 7`、`b 12` 之后，输入 `ll` 查看，最左侧的 `1、2` 就是断点号，输入 `c` ，下次运行到断点处，执行就会停下来。

```python

ipdb> ll
----> 1 a = 'michael'
      2 print(a)
      3
      4 def hello(name):
      5     print('hello')
      6     for i in range(5):
1     7         print(i)
      8     print(name)
      9
     10
     11 hello('michael')
2    12 x = 10
     13 y = 20
```

执行 `disable 1`，下次执行到「断点1」处，就不会停下来了，输入 `b` 可以查看全部断点：

```python
ipdb> b
Num Type         Disp Enb   Where
1   breakpoint   keep no    at /Users/michael/Code/00-Temp/b.py:7
2   breakpoint   keep yes   at /Users/michael/Code/00-Temp/b.py:12
```

如果想清除「断点2」，那么执行 `cl 2` 即可：

```python
ipdb> cl 2
Deleted breakpoint 2 at /Users/michael/Code/00-Temp/b.py:12
```

### run 示例

```python
ipdb> run --name michael
Restarting b.py with arguments:
	--name michael
> /Users/michael/Code/00-Temp/b.py(1)<module>()
----> 1 a = 'michael'
      2 print(a)
      3

ipdb> import sys
ipdb> sys.argv
['b.py', '--name', 'michael']
```

看到上面的结果，确实等价于 `python b.py --name michael`。

## 注意点

在 `ipdb` 调试窗口，虽然可以随时给变量赋值，但是要注意变量的名字和 ipdb 的命令不能重复了，否则会出错，可以用 `!b=123` 来规避这个问题。

## 案例

### 背景

遇到这个错误：

```python
AttributeError: 'module' object has no attribute 'processor'
```

网上有相关问题：
- https://stackoverflow.com/questions/1250103/attributeerror-module-object-has-no-attribute

主要是两种常见的错误会造成这个：

1. 模块命名和标准模块命名冲突了；
2. 循环引用

### 分析

搜索项目中哪些地方引入 `platform`

```shell
grep -rIin 'import platform'
```

同时，还可以在使用 `platform.processor()` 方法前面加上 `print(paltform.__file__)`

查看启动 Python 时，就自动加载的模块：

```python
python -v
```

或者

```python
import sys
sys.modules['platform']
```

查看运行编包命令时，加载的模块：

```python
python -v /usr/bin/pkg
```

将以上输出对比了一下，先排除标准模块会引入 `platform` ，因为标准模块，一般都是「自包含」。然后和 `python -v` 对比，发现编包命令多加载了一个第三方 `blessings` 模块，是不是它引入 `platform` 的呢？

发现，正常的构建机器上，这个模块是 `1.6.1` 版本，而目前发现问题的机器，这个包版本是 `1.7` 版本。那么，来看看，他俩之间的差异：

[Github-erikrose/blessings](https://github.com/erikrose/blessings)包，点击分支，查看 Tag 1.7 的提交记录，发现了这个[提交](https://github.com/erikrose/blessings/commit/07f24bd51167077da6e8eff35ac14f520a35b674#diff-52a399b218b9208cade790a26255db6b)

```python
from platform import python_version_tuple
```

1.7 版本中，这一行删除了，所以发生了差异。

根本原因：

```python
pkgutil.iter_modules(package.__path__)
```

### 相关问题

- [SOF-AttributeError: 'module' object has no attribute](https://stackoverflow.com/questions/1250103/attributeerror-module-object-has-no-attribute)

## 最后

学完这个 ipdb 的调试，再回头看看 Pycharm 的 debug 功能，貌似都能理解了！

## 参考

- [使用IPDB调试Python代码](https://xmfbit.github.io/2017/08/21/debugging-with-ipdb/)
- [IBM-Python 代码调试技巧](https://www.ibm.com/developerworks/cn/linux/l-cn-pythondebugger/)
- [油管-Introduction to the Python Debugger](https://www.youtube.com/watch?v=7Vmik1M_ry0)