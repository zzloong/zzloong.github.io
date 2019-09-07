---
title: Python 实用技巧总结
date: 2019-09-07 10:10:57
categories: Python
tags:
    - Tips
keywords:
    - 技巧
---

## 模块相关

1. 导入模块时，可以通过模块的 `__file__` 属性查看模块所在磁盘的路径位置，参考：[关于Python包和模块的10个知识清单](https://foofish.net/package-module-tips.html)

## Pip

### 安装Pip

方法一：

```shell
sudo apt-get purge python-pip python3-pip # Ubuntu卸载
wget https://bootstrap.pypa.io/get-pip.py
sudo python3 get-pip.py # 安装py3对应的pip
sudo python get-pip.py # 安装py2对应的pip
```

方法二：

```shell
## centos yum方式
yum install -y python-setuptools
easy_install pip
```

### Pip命令

```shell
python -m pip install -U pip #升级pip：
pip list --outdate #查看哪些包有更新：
pip install --upgrade requests  // mac,linux,unix 在命令前加 sudo -H  升级一个包：
pip install -U setuptools    #
conda update setuptools  #
pip freeze --local | grep -v '^\-e' | cut -d = -f 1  | xargs pip install -U # 升级所有包
pip show requests # 查看 package 所在的位置等信息
```

pip的配置文件中：

```shell
[list]                                                # pip list命令接口的展示方式设置
format=columns
```

参考：

- [Python小技巧](http://shadow000902.space/2018/03/06/Python%E5%B0%8F%E6%8A%80%E5%B7%A7/)
- [在Linux上安装Python 3](https://www.jianshu.com/p/81b648b1d572)
- [linux下python开发环境之一——安装python](http://www.cnblogs.com/chenzehe/archive/2010/10/20/1856437.html)
- [关于pip安装时提示pkg_resources.DistributionNotFound 错误问题](http://www.suohi.cc/posts/578ee7ff1f6d1303e9f04a5d)

### Pip源

```shell
~/.pydistutils.cfg  # 配置distutils的源，easy_install 命令会走这里配置的 pip 源
```

参考：

- [修改pip/setup.py的源](http://everet.org/python-pypi-source.html)

## 实用

### Python一键搭建Http服务器

你如果想快速且简单地共享一个目录中的文件，你只需在终端执行下面对应版本的指令：

```python
python -m http.server 8000 # Py3
python -m SimpleHTTPServer 8000 # Py2
```

- [Python 技巧](https://www.vimiix.com/python_tricks/#4)

### virtualenv创建虚拟环境

```shell
virtualenv env
```

运行带 `--no-site-packages` 选项的 virtualenv 将不会包括全局安装的包。 这可用于保持包列表干净，以防以后需要访问它。（**这在 virtualenv 1.7及之后是默认行为**）

{% note warning no-icon %}
   --no-site-packages    DEPRECATED. Retained only for backward compatibility.
                        Not having access to global site-packages is now the
                       default behavior.
{% endnote %}

参考：[Pipenv & 虚拟环境](https://pythonguidecn.readthedocs.io/zh/latest/dev/virtualenvs.html)

## ipyhon

`hist` 显示之前的代码，不显示行号，方便复制

## jupyter notebook

```shell
pip install jupyter_contrib_nbextensions && jupyter contrib nbextension install
```

安装好 jupyter notebook 插键之后，可以在 `Nbextensions` 菜单选项卡中勾选启用相关扩展功能，下面是比较好用的扩展：

- Table of Contents：更容易导航
- Autopep8：轻轻一击就能获得简洁代码
- variable inspector：跟踪你的工作空间
- ExecuteTime：显示单元格的运行时间和耗时
- 隐藏代码输入：隐藏过程，展示结果

参考：[机器之心——我知道你会用Jupyter Notebook，但这些插件你都会了吗？](https://www.jiqizhixin.com/articles/2018-12-20-12)
