---
title: 使用 docker-compose 安装搭建 RabbitMQ 集群
date: 2019-06-07 20:49:11
categories: 容器
tags:
  - Docker
  - 容器
  - 消息队列
keywords:
  - docker
  - rabbitmq
  - 容器
  - 镜像
  - cluster
---

![](https://ws1.sinaimg.cn/large/6d9475f6ly1g3t3f9mb01j20hs0but9z.jpg)

本文介绍如何利用 Docker 方式创建 rabbitmq 集群。

<!-- more -->

## rabbitmq 介绍

在利用 Docker 创建 rabbitmq 容器之前，先了解 rabbitmq 的基础知识。

### 集群模式

RabbitMQ 的 Cluster 集群模式一般分为两种，「普通模式」和「镜像模式」。

1. 普通模式：默认的集群模式，以两个节点（rabbit01、rabbit02）为例来进行说明。对于 Queue 来说，消息实体只存在于其中一个节点 rabbit01（或者 rabbit02），rabbit01 和 rabbit02 两个节点仅有相同的元数据，即队列的结构。当消息进入 rabbit01 节点的 Queue 后，consumer 从 rabbit02 节点消费时，RabbitMQ 会临时在 rabbit01、rabbit02 间进行消息传输，把 A 中的消息实体取出并经过 B 发送给 consumer。所以 consumer 应尽量连接每一个节点，从中取消息。即对于同一个逻辑队列，要在多个节点建立物理 Queue。否则无论 consumer 连 rabbit01 或 rabbit02，出口总在 rabbit01，会产生瓶颈。当 rabbit01 节点故障后，rabbit02 节点无法取到 rabbit01 节点中还未消费的消息实体。如果做了消息持久化，那么得等 rabbit01 节点恢复，然后才可被消费；如果没有持久化的话，就会产生消息丢失的现象。

2. 镜像模式：将需要消费的队列变为镜像队列，存在于多个节点，这样就可以实现 RabbitMQ 的 HA 高可用性。作用就是消息实体会主动在镜像节点之间实现同步，而不是像普通模式那样，在 consumer 消费数据时临时读取。缺点就是，集群内部的同步通讯会占用大量的网络带宽。

### 节点类型

- RAM node：内存节点将所有的队列、交换机、绑定、用户、权限和 vhost 的元数据定义存储在内存中，好处是可以使得像交换机和队列声明等操作更加的快速
- Disk node：将元数据存储在磁盘中，单节点系统只允许磁盘类型的节点，防止重启 RabbitMQ 的时候，丢失系统的配置信息

{% note warning no-icon %}
RabbitMQ 要求在集群中至少有一个磁盘节点，所有其他节点可以是内存节点，当节点加入或者离开集群时，必须要将该变更通知到至少一个磁盘节点。如果集群中唯一的一个磁盘节点崩溃的话，集群仍然可以保持运行，但是无法进行其他操作（增删改查），直到节点恢复。
解决方案：设置两个磁盘节点，至少有一个是可用的，可以保存元数据的更改。
{% endnote %}

### 安装知识点

- 建立集群时，节点中的 Erlang Cookie 值要一致，默认情况下，文件在 `/var/lib/rabbitmq/.erlang.cookie`。
- erlang 是通过主机名来连接服务，必须保证各个主机名之间可以 ping 通。可以通过编辑 `/etc/hosts` 来手工添加主机名和 IP 对应关系。如果主机名 ping 不通，rabbitmq 服务启动会失败。

```shell
$ rabbitmqctl stop_app # 停止rabbitmq服务
$ rabbitmqctl join_cluster rabbit@node1 # node2和node1构成集群, node2必须能通过node1的主机名ping通
$ rabbitmqctl start_app # 开启rabbitmq服务
```

- `–ram` 指的是作为内存节点,要是想做为磁盘节点的话,就不用加 `–ram` 这个参数了。在 RabbitMQ 集群里，必须至少有一个磁盘节点存在

```shell
$ rabbitmqctl join_cluster --ram rabbit@node1
```

- 更改节点属性

```shell
$ rabbitmqctl stop_app  # 停止rabbitmq服务
$ rabbitmqctl change_cluster_node_type ram # 更改节点为内存节点
Turning rabbit@node2 into a ram node
$ rabbitmqctl change_cluster_node_type disc # 更改节点为磁盘节点
Turning rabbit@node2 into a disc node
$ rabbitmqctl start_app # 开启rabbitmq服务
```

- 查看集群的状态

```shell
rabbitmqctl cluster_status
```

- 开启 web 管理工具

cluster 搭建起来后若在 web 管理工具中 rabbitmq_management 的 Overview 的 Nodes 部分看到 `Node statistics not available` 的信息，说明在该节点上web管理插件还未启用。 直接在显示提示信息的节点上运行：

```shell
rabbitmq-plugins enable rabbitmq_management
```

## docker-compose

基于 [bitnami/bitnami-docker-rabbitmq](https://github.com/bitnami/bitnami-docker-rabbitmq) 镜像，在一台机器上可以创建一个 rabbitmq cluster：

docker-compose.yml 内容：

```shell
version: '2'
services:
  stats:
    image: bitnami/rabbitmq:3.7
    environment:
      - RABBITMQ_NODE_TYPE=stats
      - RABBITMQ_NODE_NAME=rabbit@stats
      - RABBITMQ_ERL_COOKIE=s3cr3tc00ki3
    ports:
      - '15672:15672'
    volumes:
      - 'rabbitmqstats_data:/bitnami'
  queue-disc1:
    image: bitnami/rabbitmq:3.7
    environment:
      - RABBITMQ_NODE_TYPE=queue-disc
      - RABBITMQ_NODE_NAME=rabbit@queue-disc1
      - RABBITMQ_CLUSTER_NODE_NAME=rabbit@stats
      - RABBITMQ_ERL_COOKIE=s3cr3tc00ki3
    volumes:
      - 'rabbitmqdisc1_data:/bitnami'
  queue-ram1:
    image: bitnami/rabbitmq:3.7
    environment:
      - RABBITMQ_NODE_TYPE=queue-ram
      - RABBITMQ_NODE_NAME=rabbit@queue-ram1
      - RABBITMQ_CLUSTER_NODE_NAME=rabbit@stats
      - RABBITMQ_ERL_COOKIE=s3cr3tc00ki3
    volumes:
      - 'rabbitmqram1_data:/bitnami'

volumes:
  rabbitmqstats_data:
    driver: local
  rabbitmqdisc1_data:
    driver: local
  rabbitmqram1_data:
    driver: local
```

docker-compose 语法格式：

```shell
docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]`
```

运行 `docker-compose up -d` 运行，如果 docker-compose.yml 命名为其他名字，可以通过 `-f` 参数指定文件。该镜像默认账号密码是 ：`user/bitnami`

如上方式，会在主机上创建若干 volume，下面是一些关于 volume 的操作：

```shell
删除不使用的本地 volumes
docker volume prune
docker volume ls
docker volume rm <volume name>
```

如上创建好之后，是属于「普通模式」的集群。如果要设置为「镜像模式」，可以参考 [Rabbitmq镜像集群部署](https://blog.csdn.net/winy_lm/article/details/81128181)，比较简单，在管理界面配置：

![](http://img.mknight.cn/medivh/1553134452790.png)

- Virtual host： 可选参数，针对指定vhost下的queue进行设置
- Name: policy的名称
- Pattern: queue的匹配模式(正则表达式)
- Definition：镜像定义，包括三个部分ha-mode, ha-params, ha-sync-mode
  - ha-mode:指明镜像队列的模式，有效值为 all/exactly/nodes
    - all：表示在集群中所有的节点上进行镜像
    - exactly：表示在指定个数的节点上进行镜像，节点的个数由ha-params指定
    - nodes：表示在指定的节点上进行镜像，节点名称通过ha-params指定
  - ha-params：ha-mode模式需要用到的参数
  - ha-sync-mode：进行队列中消息的同步方式，有效值为automatic和manual
- priority：可选参数，policy的优先级

## 其他方式

### 创建容器节点

```shell
docker run -d --hostname rabbit1 --name myrabbit1 -p 15672:15672 -p 5672:5672 -e RABBITMQ_ERLANG_COOKIE='rabbitcookie' rabbitmq:3.6.15-management
docker run -d --hostname rabbit2 --name myrabbit2 -p 5673:5672 --link myrabbit1:rabbit1 -e RABBITMQ_ERLANG_COOKIE='rabbitcookie' rabbitmq:3.6.15-management
docker run -d --hostname rabbit3 --name myrabbit3 -p 5674:5672 --link myrabbit1:rabbit1 --link myrabbit2:rabbit2 -e RABBITMQ_ERLANG_COOKIE='rabbitcookie' rabbitmq:3.6.15-management
```

- 多个容器之间使用 `--link` 连接，此属性不能少；`--link <docker-name or docker-id>:alias`,`alias` 是源容器在 link 下的别名
- Erlang Cookie值必须相同，也就是 `RABBITMQ_ERLANG_COOKIE` 参数的值必须相同；

要想知道 Erlang Cookie 位置，首先要取得 RabbitMQ 启动日志里面的 `home dir` 路径，作为根路径。使用：`docker logs 容器名称` 查看。

### 加入 RabbitMQ 节点到集群

设置节点1：

```shell
docker exec -it myrabbit1 bash
rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl start_app
exit
```

设置节点2，加入到集群：

```shell
docker exec -it myrabbit2 bash
rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl join_cluster --ram rabbit@rabbit1
rabbitmqctl start_app
exit
```

- 参数 `--ram` 表示设置为内存节点，忽略次参数默认为磁盘节点。

设置节点3，加入到集群：

```shell
docker exec -it myrabbit3 bash
rabbitmqctl stop_app
rabbitmqctl reset
rabbitmqctl join_cluster --ram rabbit@rabbit1
rabbitmqctl start_app
exit
```

设置好之后，使用 `http://物理机ip:15672` 进行访问了，默认账号密码是 `guest/guest`

## 参考

MQ 安装：

- [CentOs7.3 搭建 RabbitMQ 3.6 Cluster 集群服务与使用](https://segmentfault.com/a/1190000010702020)
- [王磊的博客-RabbitMQ系列（五）使用Docker部署RabbitMQ集群](https://www.cnblogs.com/vipstone/p/9362388.html)
- [docker-compose配置rabbitmq集群服务器](https://blog.csdn.net/fqydhk/article/details/80624547)
- [官宣-Clustering Guide](https://www.rabbitmq.com/clustering.html#peer-discovery-how-does-it-work)
- [RabbitMQ 集群部署](http://mknight.cn/RabbitMQ%20%E9%9B%86%E7%BE%A4%E9%83%A8%E7%BD%B2.html)

MQ 使用：

- [rabbbitmq 堆积分析](https://ddatsh.com/post/dev/mq/rabbitmq-block/)
- [RabbitMQ使用教程（四）如何通过持久化保证消息99.99%不丢失？](https://juejin.im/post/5cef3cc36fb9a07f070e1057)
