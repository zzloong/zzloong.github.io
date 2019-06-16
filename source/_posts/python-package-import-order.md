---
title: Python 模块的加载顺序
date: 2018-12-15 22:50:18
tags:
categories: Python
keywords: python package import order
---

![](https://ws1.sinaimg.cn/large/6d9475f6ly1fy7u3g3083j20hs08w754.jpg)

<!-- more -->

## 基本概念

### module
模块， 一个 py 文件或以其他文件形式存在的可被导入的就是一个模块

### package
包，包含有 __init__ 文件的文件夹

### relative path
相对路径，相对于某个目录的路径

### absolute path
绝对路径，全路径

## Python 解释器是如何查找包和模块的

Python 执行一个 py 文件，无论执行的方式是用绝对路径还是相对路径，interpreter 都会把文件所在的 directory 加入 sys.path 这个 list 中，并且是索引为 0 的位置。Python 就是在 sys.path 中查找包和模块的。

```python
# test.py

# coding:utf-8
import sys
print(sys.path)
print('Now in main.py')


def hello():
    print('michael hello')


if __name__ == '__main__':
    hello()

# 执行 python test.py
$ python test.py
['/tmp/module-package/app', '/usr/lib64/python27.zip', '/usr/lib64/python2.7', '/usr/lib64/python2.7/plat-linux2', '/usr/lib64/python2.7/lib-tk', '/usr/lib64/python2.7/lib-old', '/usr/lib64/python2.7/lib-dynload', '/usr/lib64/python2.7/site-packages', '/usr/lib/python2.7/site-packages']
Now in test.py
michael hello
```

## Python 解释器查找包的顺序

解释器查找包：

- 解释器会默认加载一些 modules，除了`sys.builtin_module_names` 列出的内置模块之外，还会加载其他一些标准库，都存放在`sys.modules`字典中。
- 然后就是搜索 `sys.path` 路径下的模块了。

```python
In [3]: import sys

In [4]: print(sys.builtin_module_names)
('_abc', '_ast', '_codecs', '_collections', '_functools', '_imp', '_io', '_locale', '_operator', '_signal', '_sre', '_stat', '_string', '_symtable', '_thread', '_tracemalloc', '_warnings', '_weakref', 'atexit', 'builtins', 'errno', 'faulthandler', 'gc', 'itertools', 'marshal', 'posix', 'pwd', 'sys', 'time', 'xxsubtype', 'zipimport')
```

这样的查找顺序将会导致同名包或模块被遮蔽。

示例2：
```python
# tree
$ tree . -L 1
.
├── __init__.py
├── name
├── os.py
├── test2.py
├── test.py
└── test.pyc

# test2.py
import os
from redis import Redis
from test import hello

print('Now in test2.py')
print(os.getcwd())

# 执行 python test2.py
$ python test2.py
Traceback (most recent call last):
  File "test2.py", line 2, in <module>
    from redis import Redis
ImportError: No module named redis
```

这里的 `os` 模块并不是是 `built-in module`，上面已经将 `sys.builtin_module_names` 内容打印出来了。只是 Python 解释器启动时就加载到了 `sys.modules`中缓存起来了。所以，即使在同目录下有同名模块，解释器依然是可以找到正确的 `os` 模块的！如果你在`import os`之前，先执行`del sys.modules['os']`，那么，标准模块 `os` 就会被同目录下的 `os.py` 屏蔽了。

`redis` 属于第三方模块，默认安装位置是 Python 环境变量中的 `site-packages`，解释器启动之后，会将此目录加到 `sys.path`，由于当前目录会在 `sys.path` 的首位，当前目录的 redis 优先被找到了，`site-packages` 中的 `redis` 模块被屏蔽了。

综上所述，搜索的一个顺序是：`sys.modules` 缓存 -> `sys.path[0]` 即当前目录查找 -> `sys.path[1:]`路径查找。

同时发现，模块被加载的时候，其中非函数或类的语句，例如 `print('hello')`、`name=michael`等，是会在 `import`的时候，默认就执行了。

## 交互式执行环境的查找顺序

交互执行环境，解释器会自动把当前目录加入到`sys.path`，这一点和直接执行文件是一样的，但是这种方式下，`sys.path[0]` 是存储的当前目录的相对路径，而不是绝对路径。

```python
In [4]: import sys

In [5]: sys.path[0]
Out[5]: ''
```

## 模块中的 `__file__` 变量

### 文件中的 `__file__`

当模块以文件的形式出现 __file__ 指的是模块文件的路径名，以相对路径执行 __file__ 是相对路径，以绝对路径执行 __file__ 是绝对路径：

```python
# test3.py
print __file__

# 执行 python test.py
$ python test3.py
test3.py

$ python /tmp/module-package/app/test3.py
/tmp/module-package/app/test3.py
```

### 交互式 Shell 中的 `__file__`

前交互式 Shell 的执行并不是以文件的形式加载，所以不存在 `__file__` 这样的属性：

```python
In [8]: __file__
---------------------------------------------------------------------------
NameError                                 Traceback (most recent call last)
<ipython-input-8-358d5687b810> in <module>()
----> 1 __file__

NameError: name '__file__' is not defined
```

## `sys.argv[0]` 变量

`sys.argv[0]` 是获得入口执行文件路径，`__file__` 是真实被执行模块的文件路径。比如下面例子中，`test2.py` 就是入口执行文件，而 `test.py` 就是在 `import` 时真实被执行的模块

```python
# test.py
print(__file__)
print(sys.argv[0])

# test2.py
import test

# 执行 python test2.py
/tmp/module-package/app/test.py # __file__
test2.py # sys.argv[0]
```

## `sys.modules` 的作用

载入的模块存放在何处？ 答案是 `sys.modules`。 模块一经载入， Python 会把这个模块加入 `sys.modules` 中供下次载入使用，这样可以加速模块引入，起到缓存作用。`sys.modules` 是一个 `dict` 类型的值。

```python
In [14]: sys.modules['requests']
---------------------------------------------------------------------------
KeyError                                  Traceback (most recent call last)
<ipython-input-14-8aefaef0aed5> in <module>()
----> 1 sys.modules['requests']

KeyError: 'requests'

In [15]: import requests

In [16]: sys.modules['requests']
Out[16]: <module 'requests' from '/usr/lib/python2.7/site-packages/requests/__init__.pyc'>
```

```python
# 没有预先引入 math，但是 sys.modules 中已经有这个键
In [18]: sys.modules['math']
Out[18]: <module 'math' from '/usr/lib64/python2.7/lib-dynload/math.so'>
```

需要注意的是， `sys.modules['math']`  尽管可以看到 `math` 键，但是，要使用它，还是需要显示 `import math` 之后才能使用的，因为那只是 Python 解释器后台缓存的，你不显示引入，本地空间还是不会去发现它。

## 总结

Python 通过查找 `sys.path` 来决定包的导入，Python解释器启动时加载的模块缓存 > 同级目录 > `sys.path[1:]`。Python 中的特有属性 `__file__` 以及 `sys.argv[0]`、`sys.argv[0]`、`sys.modules` 可以帮助分析包的查找和导入过程。

解决这个问题，请教了大牛同事，果然一下子让我明白了。于是，自问自答了在 SegmentFault 上提的问题：
- [Python 包的引入顺序到底是怎样的？](https://segmentfault.com/q/1010000017357057)

## 参考
- [三月沙-如何理解 Python 的模块查找原理与方式](https://sanyuesha.com/2016/05/28/python-module-path-find/) 本文内容主要参考，但是该文章中提到的`os`属于`built-in moulde`的理解是有误的，本文中修正了理解。
- [构建一个模块的层级包](https://python3-cookbook.readthedocs.io/zh_CN/latest/c10/p01_make_hierarchical_package_of_modules.html)
- [The Python Standard Library](https://docs.python.org/3/library/)
- [Medium-Python 的 Import 陷阱](https://medium.com/pyladies-taiwan/python-%E7%9A%84-import-%E9%99%B7%E9%98%B1-3538e74f57e3)
- [CSDN-Python 模块搜索路径](https://blog.csdn.net/liang19890820/article/details/76219560) 提交了 `PYTHONPATH` 这个环境变量的作用
- [librarybook-The sys module](http://effbot.org/librarybook/sys.htm)
- [官宣-System-specific parameters and functions](https://docs.python.org/3.7/library/sys.html)