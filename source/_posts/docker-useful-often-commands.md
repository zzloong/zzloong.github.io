---
title: Docker 入门指南——常用命令
date: 2019-06-02 14:13:44
categories: 容器
tags: Docker
keywords:
  - docker
  - commands
---

![](https://ws1.sinaimg.cn/large/6d9475f6ly1g3mthif5qvj20iu09l0t6.jpg)

Docker 容器学习笔记系列：

- [Linux--CentOS 安装 Docker 教程](https://michael728.github.io/2019/06/01/docker-centos-install/)
- [Docker 入门指南——常用命令](https://michael728.github.io/2019/06/02/docker-useful-often-commands/)
- [使用容器 Docker 创建开发环境](https://michael728.github.io/2019/06/02/docker-create-develop-environment/)

前面已经介绍了 Docker 的安装方式，本文总结一下使用 Docker 的基本概念和常用命令。

<!-- more -->

## 基本概念

### 镜像 Image
镜像是一些打包好的已有的环境，可以被用来启动和创建容器

### 容器  Container
容器是镜像的实例化

### 容器的UUID
UUID -- 通用唯一标识符（`Universally Unique Identifier`）

容器有三种方式来进行标识：
- 长UUID
- 短UUID
- Name

UUID 是 Docker daemon 产生的，在一台主机上是唯一的，在创建容器的时候可以通过 `--name` 来指定容器的名字，如果不指定会自动分配一个字符串名称。

通过 `docker ps`、`docker inspect` 等命令可以查看到容器的标识信息

### 容器启动过程

1. 检查本地是否存在指定的镜像，不存在就从公有仓库下载
2. 利用镜像创建并启动一个容器
3. 分配一个文件系统，并在只读的镜像层外面挂载一层可读写层
4. 从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
5. 从地址池配置一个 ip 地址给容器
6. 执行用户指定的应用程序
7. 执行完毕后容器被终止

## 镜像

- 查看 `image` 列表：

```
docker iamges
```

- 下载镜像：

```
docker pull registry.domain.com/library/ubuntu:14.04
```

### 制作镜像

```
# 常用下面这种方式制作镜像，Dockerfile 文件更透明
docker build [-f DockerfileName] -t image_name DockerfilePath
```

这里 `DockerfilePath` 是 Context 上下文目录，在创建的时候会全部上传到 Docker Server 端，所以这个目录不要太大。

参数说明：

- `-f`/`--file`：Name of the Dockerfile (Default is 'PATH/Dockerfile')，Dockerfile的完整路径
- `-t`/`--tag`：Name and optionally a tag in the 'name:tag' format，指定了镜像名称，镜像的名字及 tag，通常 `name:tag` 或者 `name` 格式
- `--no-cache`：Do not use cache when building the image，[这篇文章](https://www.centos.bz/2016/11/rebuild-docker-image-without-cache/)介绍了使用这个参数的场景，构建镜像中有时候包含 `git clone` 命令，会默认使用缓存，新代码就不会下载了，所以，有时候需要加上这个参数；
- `--pull`，默认 `false`。Always attempt to pull a newer version of the image，设置该选项，总是尝试 pull 镜像的最新版本

其他的 `build` 参数，可以采用 `docker build -h` 查看。

### 删除镜像

删除 `image` 之前，需要先删除 `container`:
```
docker ps -a
docker rm container_id/container_name
```

删除 `image`:
```
docker rmi <image-id>
docker rmi <image-name>:<tag>
```

删除虚悬镜像(`dangling image`)：
```
$docker image ls -f dangling=true #列出虚悬镜像

$ docker image prune
WARNING! This will remove all dangling images.
Are you sure you want to continue? [y/N] y
```

### 无tag镜像(dangling)

显示无 tag 镜像：
```
$ docker images --filter "dangling=true"
```

当新构建的镜像占用这个镜像ID的 `repo:tag` 时，会出现这些图像，将其保留为 `：` 或 `untagged`。可以使用如下命令批量删除这类镜像
```shell
$ docker rmi $(docker images -f "dangling=true" -q)
```

### 迁移镜像

保存镜像到文件，语法格式：
```
docker save [OPTIONS] IMAGE [IMAGE...]
```

例如：
```
docker save image_name -o file.tar
# Or
docker save image_name --output file.zip
```

将镜像保存一个 `tar` 包文件了，也可以是 `zip` 格式的压缩包。

加载一个 `tar` 包的镜像：
```
docker load -i file.tar
```

## 容器操作

- 查看运行中的容器
```
docker ps
```

- 查看所有容器
```
docker ps -a
```

- 显示运行的容器里的进程信息
```
docker top cid
```
此处， cid 表示你运行的容器名

- 显示容器详细信息
```
docker inspect cid
```

- 日志查看
```
docker logs cid
# 实时查看日志输出
docker logs -f cid
```

-  查看容器root用户密码
```
docker logs cid 2>&1 | grep '^User: ' | tail -n1
```

### 容器运行

语法格式：
```
Usage:	docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```

例如：
```
docker run -it --name cidregistry.domain.com/library/ubuntu:14.04
```

如果直接 `docker run -it registry.domain.com/library/ubuntu` 可能会出错，因为不加 `tag` ，默认就去运行 `latest` 版本，而本地没有 `latest` 版本，所以，需要将 `image`+`tag`，以冒号分隔拉去。

- `-i` 交互操作
- `-t` 终端
- `--name` 指定容器名字
- `-d` 后台运行一个容器
- `--rm`，表明退出容器后随之将其删除，可以避免浪费空间
- `-p` 映射端口
- `-v` 挂载 volumn
- `--privileged=false` 容器内 root 拥有真正 root 权限

当处于一个容器中时，利用`exit`退出容器

- 在容器中运行一段程序

```
docker run ubuntu apt-get update
```

### 启动已终止（stop）容器：
```
docker restart 3e8 # 3e8 为容器的 id 号，不需要全写，也可以用容器名替代
```

### 进入容器

- 进入正在运行的容器，退出不会造成容器停止：
```
docker exec -it cid /bin/bash
```

- 附着到正在运行的容器中，退出时会导致容器终止，不常用：
```
docker attach cid
```

### 从容器拷贝文件出来

拷贝文件出来
```
docker cp cid:/container_path to_path
```

### 删除容器

```
docker rm cid
# 强制删除
docker rm -f cid
# 删除所有容器
# -q 表示只列出容器的 id 值
docker rm `docker ps -a -q`
```

### 容器运行状态修改

```
docker start/stop/kill/restart cid
```

### 更改容器名字

```
docker rename old new
```

## 修改容器，制作镜像

`image` 相当于类，`container` 相当于实例，不过可以动态给实例安装新软件，然后把这个 `container` 用 `commit` 命令固化成一个 `image`：
```shell
docker commit -m "修改yum源" -a "michaelxiang" cid registry.domain.com/ci/centos-os:latest
```

这种制作镜像的方式并不推荐，因为不如 Dockerfile 方式透明。

## 推送镜像

```shell
docker login registry.domain.com -u username -p password
docker push registry.domain.com/michael/IMAGE[:TAG]
```

如果支持 accesstoken 权限认证：

```shell
docker login registry.domain.com -u test -p ACCESSTOKEN:XXXXXXXXXXXXX:ACCESSTOKEN
```

## docker-compose

它允许用户通过一个单独的 `docker-compose.yml` 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。

Compose 中有两个重要的概念：

- 服务 (service)：一个应用的容器，实际上可以包括若干运行相同镜像的容器实例。
- 项目 (project)：由一组关联的应用容器组成的一个完整业务单元，在 docker-compose.yml 文件中定义。

Compose 的默认管理对象是项目，通过子命令对项目中的一组容器进行便捷地生命周期管理

- [如何写docker-compose.yml，Docker compose file 参考文档](https://deepzz.com/post/docker-compose-file.html)
- [官宣-Overview of docker-compose CLI](https://docs.docker.com/compose/reference/overview/)
- [Docker 三剑客之 Compose 项目](https://yeasy.gitbooks.io/docker_practice/compose/commands.html)

## 其他

你可以通过以下命令来便捷的查看镜像、容器、数据卷所占用的空间：

```shell
docker system df
```

## docker 配置

### daemon.json 文件

```shell
{
    "insecure-registries": ["registry.xxx.com"],
    "registry-mirrors": ["https://xxxx.mirror.domain.com"],
    "exec-opts": ["native.cgroupdriver=systemd"],
    "storage-driver": "overlay2",
    "storage-opts": [
                "overlay2.override_kernel_check=true"
    ]
}
```

## FAQ

### Q1：Docker 空间问题

Docker长时间运行后的volumes目录清理

- [悟能-Docker长时间运行后的volumes目录清理](https://blog.lutty.me/linux/2017-07/docker-clean-volumes-dir.html)

## 参考

命令：

- [官宣——Command-Line Interfaces (CLIs)](https://docs.docker.com/engine/reference/run/) 强烈
推荐
- [Docker 使用总结](http://yansu.org/2017/02/21/docker-summary.html) 本文的主要参考
- [Docker 入门教程](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
- [Docker —— 从入门到实践](https://legacy.gitbook.com/book/yeasy/docker_practice/details)
- [只要一小时，零基础入门Docker](https://zhuanlan.zhihu.com/p/23599229)
- [SF-docker命令详解](https://segmentfault.com/a/1190000008876540#articleHeader4)
- [简书-Docker命令使用](https://www.jianshu.com/p/d1f5ac9f9d4e)
- [Docker命令行参考(8) – docker images列出镜像](https://www.centos.bz/2017/01/docker-images-list-images/) 删除无 tag 镜像

镜像登录：

- [docker搭建私有仓库、自签发证书、登录认证](https://segmentfault.com/a/1190000012175537)

容器间的通信：

- [Docker容器与宿主机同网段互相通信](http://www.louisvv.com/archives/695.html)
- [关于对docker run --link的理解](https://www.jianshu.com/p/21d66ca6115e)
- [cizixs-docker 容器的网络模式](https://cizixs.com/2016/06/12/docker-network-modes-explained/)
