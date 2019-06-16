---
title: Linux——CentOS 安装 Docker 教程
date: 2019-06-01 17:54:52
categories: 容器
tags:
 - docker
keywords:
  - docker
  - 容器
  - 安装
  - centos
  - linux
---

![](https://ws1.sinaimg.cn/large/6d9475f6ly1g3m4o85kngj20hs0buwg3.jpg)

Docker 容器学习笔记系列：

- [Linux--CentOS 安装 Docker 教程](https://michael728.github.io/2019/06/01/docker-centos-install/)
- [Docker 入门指南——常用命令](https://michael728.github.io/2019/06/02/docker-useful-often-commands/)
- [使用容器 Docker 创建开发环境](https://michael728.github.io/2019/06/02/docker-create-develop-environment/)

本文主要介绍 CentOS 系统安装 Docker 的流程。

<!-- more -->

## 前提条件

### OS 要求

CentOS7:

> The centos-extras repository must be enabled. This repository is enabled by default, but if you have disabled it, you need to [re-enable](https://wiki.centos.org/AdditionalResources/Repositories) it.
> The overlay2 storage driver is recommended

### 卸载旧的版本

较旧版本的 Docker 被称为 `docker` 或 `docker-engine`。如果已安装这些，请卸载它们以及相关的依赖项：

```shell
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```

`/var/lib/docker` 目录下保存着镜像、容器、卷、网络。官方文档安装的 `docker-ce` 包，内部源中只有 `docker-engine`包，`docker-ce` 是最新的社区版本的包名。

## 安装 Docker CE

### 使用源安装

#### 设置源

1.安装依赖的包，`yum tils` 提供了 `yum-config-manager` 套件， `device-mapper-persistent-data` 和 `lvm2` 是 `devicemapper` 存储驱动所依赖的包。

```shell
$ sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

2.使用如下命令设置 stable 源：

```shell
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

国内可选择[清华大学源-Docker Community Edition 镜像使用帮助](https://mirrors.tuna.tsinghua.edu.cn/help/docker-ce/)

#### 安装 Docker CE

1.安装最新版本的 Docker CE：
```
$ sudo yum install docker-ce
```

2.要安装特定版本的 Docker CE，可在 repo 中列出可用版本，然后选择并安装：
```
$ yum list docker-ce --showduplicates | sort -r
```

Docker 安装之后， docker 组就被创建了，但没有用户加到这个群组中。

3.启动 Docekr：
```
$ sudo systemctl start docker
```

4.可以通过运行 `hello-world` 镜像来验证 docker 的安装成功：
```
sudo docker run hello-world`
```

这个命令下载一个测试镜像，在容器中运行它。当容器运行时，它会打印一条信息并退出。

### 从 RPM 包安装

如果无法使用 Docker的源来安装 Docker，则可以下载适用于您的发行版的 `.rpm` 文件并手动安装。每次要升级 Docker 时都需要下载新文件。

1. 去 https://download.docker.com/linux/centos/7/x86_64/stable/Packages/ 下载 rpm 包
2. 安装 Docker CE：`$ sudo yum install /path/to/package.rpm`

### 升级 Docker CE

使用 `yum -y upgrade docker-ce` 升级版本。

## 卸载

1. 卸载 Docker 包：`$ sudo yum remove docker-ce`
2. 主机上的镜像、容器、卷或自定义配置文件不会自动删除。为了删除这些文件，可以运行如下命令：`$ sudo rm -rf /var/lib/docker`

你必须手动删除任何已编辑的配置文件

### 注意点

1. 如果非 root 用户想要使用 Docker，你应该将该用户添加到 docker 组中：`sudo usermod -aG docker your-user`
2. 安装 Docker CE 之后，它在基于 `DEB` 的发行版上会自动启动。在基于 `RPM` 的发行版上，需要使用相应的 `systemctl` 或 `service` 命令手动启动它

## 使用 systemd 控制 Docker

[使用 systemd 控制 Docker](https://docs.docker.com/config/daemon/systemd/#start-the-docker-daemon)

### 手动启动

大多数 Linux 发行版使用 `systemctl` 启动服务，如果没有，就用 `service`命令：

- `systemctl`:`$ sudo systemctl start docker`
- `service`:`$ sudo service docker start`

### 系统自启

If you want Docker to start at boot, see
如果你想要实现开启自启 `docker`，可以看看这篇文章 [Configure Docker to start on boot](https://docs.docker.com/install/linux/linux-postinstall//)

```
systemctl list-unit-files|grep docker # 查看 Docker 服务状态
```

### 配置 Docker daemon 选项

推荐的方法是使用平台独立的 `daemon.json` 文件，默认位于 `/etc/docker/` 中。详细配置项，查看[官宣-Daemon configuration file](https://docs.docker.com/engine/reference/commandline/dockerd//#daemon-configuration-file)，有一份中文的备注说明：[docker daemon(dockerd)配置文件daemon.json](https://www.cnblogs.com/ningskyer/articles/8330143.html)。

你可以使用 `daemon.json` 配置几乎所有守护程序配置选项。下面的例子配置了两个选项。你不能使用 `daemon.json` 机制配置的一个选项是 `HTTP proxy`。

#### Runtime directory and storage driver

你可能想要通过移动镜像、容器和卷到独立分区来控制磁盘空间。

为了实现这个，可以在 `daemon.json` 中做如下配置：
```
{
    "data-root": "/mnt/docker-data",
    "storage-driver": "overlay"
}
```

#### HTTP/HTTPS proxy

Docker 守护进程使用 `HTTP_PROXY`, `HTTPS_PROXY` 和 `NO_PROXY` 环境变量在它的启动环境中来配置 HTTP 和 HTTPS 代理。你不能使用 `daemon.json` 文件来配置这些环境变量。

如果你使用的是 HTTP 或 HTTPS 代理服务器，例如在公司设置中，则需要将此配置添加到 Docker systemd 服务文件中。

1.为 docker 服务创建一个 systemd 目录：
```
$ sudo mkdir -p /etc/systemd/system/docker.service.d
```

2.创建一个文件 `/etc/systemd/system/docker.service.d/http-proxy.conf`，加入 `HTTP_PROXY` 环境变量：
```
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:80/"
```

或者，如果你使用 HTTPS 代理服务，创建一个文件 `/etc/systemd/system/docker.service.d/https-proxy.conf`，加入 `HTTPS_PROXY` 环境变量：
```
[Service]
Environment="HTTPS_PROXY=https://proxy.example.com:443/"
```

3.如果你拥有内部的 Docker registries 服务或者要使用[国内的镜像加速器-daocloud.io](https://www.daocloud.io/mirror)，你需要通过指定 `NO_PROXY` 环境变量来不通过代理访问它们：
```
[Service]
Environment="HTTP_PROXY=http://proxy.example.com:80/" "NO_PROXY=localhost,127.0.0.1,docker-registry.somecorporation.com,daocloud.io"
```

这样，你访问 `NO_PROXY` 中的网址时，就不会走代理，速度会比较快。

Or, if you are behind an HTTPS proxy server:
```
[Service]
Environment="HTTPS_PROXY=https://proxy.example.com:443/" "NO_PROXY=localhost,127.0.0.1,docker-registry.somecorporation.com"
```

一个实际配置的栗子：
```
mkdir -p /etc/systemd/system/docker.service.d/ # 先保证有这个目录
cat <<'EOF'>/etc/systemd/system/docker.service.d/http-proxy.conf # 这里一定要记得让内部镜像仓地址不要走代理,否则无法访问我们私有的镜像仓
[Service]
Environment="HTTP_PROXY=http://127.0.0.1:3128/"
Environment="HTTPS_PROXY=http://127.0.0.1:3128/"
Environment="NO_PROXY=localhost,127.0.0.0/8,.domain.com"
EOF
```

4.Flush changes:
```
$ sudo systemctl daemon-reload
```

5.重启 Docker：
```
$ sudo systemctl restart docker
```

6.验证配置项已经被加载：
```
$ systemctl show --property=Environment docker
Environment=HTTP_PROXY=http://proxy.example.com:80/
```

如果你采用的 HTTPS 代理：
```
$ systemctl show --property=Environment docker
Environment=HTTPS_PROXY=https://proxy.example.com:443/
```

### 手动创建 systemd 单元文件

当你手动安装 Docker 时，如果你想要用 systemd 管理 Docker，可以安装两个单元文件 `service` 和 `socket`，参考 [moby/contrib/init/systemd/](https://github.com/moby/moby/tree/master/contrib/init/systemd)，下载文件至 `/etc/systemd/system`。

## 配置使用 docker 镜像仓库

### 选择一：ustc的镜像
新版的 Docker 使用 `/etc/docker/daemon.json（Linux）` 配置 Daemon：

```
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}
```

- [USTC-Docker镜像使用帮助](https://lug.ustc.edu.cn/wiki/mirrors/help/docker)

### 选择二：Docker 中国官方镜像加速

```
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

- [Docker 中国官方镜像加速](https://www.docker-cn.com/registry-mirror)

Docker 拖取镜像默认走的是 HTTPS 协议（443端口），一般私有仓库都没有合法的 HTTPS 证书，通过通过配置私有仓库为非安全仓库：
```
{
 "insecure-registries" : ["hub.h.com"]
}
```

`insecure-registries` 就是配置的非安全仓库的地址。

测试配置的结果：

> busybox是一个集成了一百多个最常用linux命令和工具的软件,同时它也是一个最小的Linux系统，它提供了该系统的主要功能，例如grep、find、mount以及telnet等但不包含一些与GNU相关的功能和选项

```
docker pull busybox
```

## Docker 存储驱动

Linux kernel 4.0以后才支持的overlay2（Linux kernel 3.18以后才支持的叫overlayFS）。同时请确保docker的服务端版本不低于1.12，否则无法支持。`uname -sr` 可以查看系统内核版本。

`Docker 1.12.6/v17.03` 文档中 CentOS7 系统下安装时，明确说明，用于生产时，必须使用 `devicemapper` 驱动的 `direct-lvm` 模式，需要我们提前准备好块设备，以提供更好的稳定性和性能。默认使用 `devicemapper` 驱动的 `loop-lvm` 模式，因为安装简单，只适用于测试环境。从 `docker info` 信息可以看出，`loop-lvm` 模式最大可用空间只有107GB。生产环境下必须使用 `devicemapper` 驱动的 `direct-lvm` 模式，使用块设备，速度更快并且能更有效地使用系统资源。

在 `Docker v17.06` 及以后的版本中，关于 `OverlayFS` 存储驱动，尽量使用 `overlay2` 而不要使用 `overlay`，官方的说明是 `overlay` 可以使用但不建议。使用 `overlay2` 时 Linux 系统内核要求4.0以上，或者 CentOS7 的内核在 `3.10.0-693` 以上。Docker-CE v17.06 及以上，在使用 `overlay2` 驱动时，还需要设置额外的参数，以禁止检测内核为4.0版本。

```shell
#查看当前存储驱动
docker info|grep -i storage
#停止Docker
service docker stop
#清空数据，如果有啥需要的请自己备份
rm -rf /var/lib/docker/*
#修改配置文件
vi /etc/docker/daemon.json
#如果没有这个文件或没有内容，就直接把下面的粘贴进去
#不然就只添加那一条
#如果不是在最后一行加请自行在末尾添加逗号
{
 "storage-driver": "overlay2"
}
#如果是CentOS7或者RedHat7内核在3.10.0-693以下的，设置额外的参数：
{
 "storage-driver": "overlay2",
 "storage-opts": [
 "overlay2.override_kernel_check=true"
 ]
}
#当然，也可以通过给docker修改启动参数的方式来
# 1.修改/etc/init.d/docker
# 这个直接在 dockerd 后面加参数就行，不过其实和下面的差不多
# 2.修改/etc/sysconfig/docker
# 改成类似 other_args="-s overlay2"
# 3.修改/usr/lib/systemd/system/docker.service
# 改成类似 ExecStart=/usr/bin/dockerd -s overlay2

#启动docker
service docker start
```

通常在生产构建机器上，一般系统盘大小都不大，都会挂载一个较大容量的数据盘，比如 data 目录。那么，为了避免日后 Docker 的根目录 `/var/lib/mock`过大，撑爆系统盘，我们需要想办法修改一下 Docker 的根目录，主要有两种办法：

1.先备份 `/var/lib/docker`的内容，然后在创建`/data/docker`的软连接: `ln -s /data/docker /var/lib/docker`

2.修改 `daemon.json` 配置：

```shell
{
# before before 17.06-ce
"graph": "/data/docker",
# docker after 17.06-ce
"data-root":"/data/docker",
}
```

## 参考

- [官宣-Get Docker CE for CentOS](https://docs.docker.com/install/linux/docker-ce/centos/#prerequisites)
- [官宣-Control Docker with systemd-Docker代理设置](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
- [如何在CentOS 7中安装Docker](http://sheawey.com/post/how-to-install-docker-in-centos7.html) 这篇文章中提到为了使用 overlay2 必须升级 CentOS 内核，其实也可以不用的

Dockuer Hub 镜像：
- [第二天堂-Docker 设置 socks5 代理或使用国内镜像](https://blog.yanzhe.tk/2017/11/09/docker-set-proxy/)
- [伊布-国内 docker 仓库镜像对比](https://ieevee.com/tech/2016/09/28/docker-mirror.html) 文中附有一个测试镜像速度的脚本
- [阿里云-Docker 镜像加速器](https://yq.aliyun.com/articles/29941)
- [华为云-Docker Hub 服务](https://mirrors.huaweicloud.com/)

存储驱动：
- [老高的技术博客-centos升级内核版本以支持overlay2](https://blog.phpgao.com/update_linux_kernel.html)
- [深入了解Docker存储驱动](http://dockone.io/article/1765) 一个关于容器和 K8s 相关的社区
- [官宣-Use the OverlayFS storage driver](https://docs.docker.com/storage/storagedriver/overlayfs-driver/)
- [官宣-Docker storage drivers](https://docs.docker.com/storage/storagedriver/select-storage-driver/)
- [senra-Docker切换OverLay(2)——提高性能，加快速度](http://www.senra.me/docker-switch-storage-driver-to-overlay2-to-optimize-performance/)
- [CentOS7.x系统中使用Docker时，在存储方面需要注意的问题](http://blog.51cto.com/10321203/2090536)
- [运维笔记-DOCKER更改镜像存储位置](https://www.centosdoc.com/docker/53.html) 描述了常见修改存储位置的两个方法，同时，提到了新旧版本的配置项的注意点
- [SOF-How to change the docker image installation directory](https://stackoverflow.com/questions/24309526/how-to-change-the-docker-image-installation-directory/34731550#34731550) 解释了不同版本根目录配置项的区别