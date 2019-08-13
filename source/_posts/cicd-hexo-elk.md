---
title: DevOps 之 ELK 基础设施搭建
date: 2019-06-16 14:10:57
categories: DevOps
tags:
    - CICD
    - DevOps
    - ELK
keywords:
  - Elasticsearch
  - Kibana
  - Logstash
  - Filebeat
---

## 简介

## Elasticsearch

官方的[Elasticsearch Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html) 文档是相当全面了，而且，不同版本都可以切换，真是赞！如果英文的不想看，还提供了中文版的 [Elasticsearch: 权威指南](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)，虽然可能版本不是最新的，但是了解基本概念也是有帮助的。

Elasticsearch 包里自包含了 OpenJDK 的包，在 Elacticsearch 目录下的 `jdk` 目录中。如果你想要使用你自己配置好的 Java 版本，需要设置 `JAVA_HOME` 环境变量。

官方文档的 `Set up Elasticsearch` 下面有安装的指导，具体页面 [Installing Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html) 中提供了多种安装包对应的指导链接！本文就先选择 [tar 包](https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html)的方式安装。

### 下载安装包

官网的下载地址简直是龟速，发现我司的[镜像站](https://mirrors.huaweicloud.com/elasticsearch/)上提供了安装包，试用后安利，速度不错：

```shell
wget https://mirrors.huaweicloud.com/elasticsearch/7.3.0/
wget https://mirrors.huaweicloud.com/elasticsearch/7.3.0/elasticsearch-7.3.0-linux-x86_64.tar.gz.sha512
# 验证安装包的完整性，如果没问题，会输出 OK
shasum -a 512 -c elasticsearch-7.3.0-linux-x86_64.tar.gz.sha512
tar -xzf elasticsearch-7.3.0-linux-x86_64.tar.gz
cd elasticsearch-7.3.0/
```

下文的 `$ES_HOME` 就是指这里 tar 包解压后的文件夹目录，目录组成：

```shell
.
├── bin # 二进制脚本存放目录，包括 elasticsearch 来指定运行一个 node，包括 elasticsearch-plugin 来安装 plugins
├── config # 包含了 elasticsearch.yml 配置文件
├── data # 节点上分配的每个 index/分片 的数据文件
├── jdk
├── lib
├── LICENSE.txt
├── logs
├── modules
├── NOTICE.txt
├── plugins # 插键文件存放的位置
└── README.textile
```

### 使用命令行运行 Elasticsearch

```shell
./bin/elasticsearch
```

{% note warning %}

不能使用 root 账号运行上面的命令，否则会报错：

```shell
Caused by: java.lang.RuntimeException: can not run elasticsearch as root
```

{% endnote %}

### 检查一下运行状态

```shell
curl -X GET "localhost:9200/?pretty"
```

或者在浏览器中访问 `localhost:9200` 都可以，会返回：

```shell
{
  "name" : "michael-pc",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "qhudAW_ESsS9oAJru7oTOQ",
  "version" : {
    "number" : "7.3.0",
    "build_flavor" : "default",
    "build_type" : "tar",
    "build_hash" : "de777fa",
    "build_date" : "2019-07-24T18:30:11.767338Z",
    "build_snapshot" : false,
    "lucene_version" : "8.1.0",
    "minimum_wire_compatibility_version" : "6.8.0",
    "minimum_index_compatibility_version" : "6.0.0-beta1"
  },
  "tagline" : "You Know, for Search"
}
```

如果你是在服务器上部署的 ES，那么，在你的工作机上目前还无法调用通 `<IP>:9200`，需要一些配置才可以。后面会介绍。

### 作为守护进程运行

要将 ES 作为守护程序运行，请在命令行中指定 `-d`，指定 `-p` 参数，将进程 ID 记录到 `pid` 文件：

```shell
./bin/elasticsearch -d -p pid
```

日志在 `$ES_HOME/logs` 目录中。

如果想停止  ES，运行如下的命令：

```shell
pkill -F pid
```

### 在命令行配置 ES

ES 默认会加载位于 `$ES_HOME/config/elasticsearch.yml` 的配置文件。

任何能够通过配置文件设置的内容，都可以通过命令行使用 `-E` 的语法进行指定：

```shell
./bin/elasticsearch -d -Ecluster.name=my_cluster -Enode.name=node_1
```

{% note info %}
通常，任何群集范围的设置（如cluster.name）都应添加到elasticsearch.yml配置文件中，而任何特定于节点的设置（如node.name）都可以在命令行中指定
{% endnote %}

### 配置 ES

官网关于配置的内容主要有两处：

- [Important Elasticsearch configuration](https://www.elastic.co/guide/en/elasticsearch/reference/current/important-settings.html)
- [Configuraing Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html) 做了说明。

本文仅搭建一个单节点的 ES 服务，未搭建集群。

- 设置集群名称

通过 `cluster.name` 可以设置集群的名称：

```shell
cluster.name: michael-es
```

通过 `node.name` 可以配置每个节点的名称，集群中每个节点的名称都不要相同

```shell
node.name: es-node-1
```

- 设置访问的地址和端口

我们需要设定 ES 运行绑定的 Host，默认是无法公开访问的！如果设置为主机的公网 IP 或 `0.0.0.0` 就是可以公开访问的，可以都设置为公开访问或者部分主机公开访问。如果是公开访问就配置为：

```shell
network.host: 0.0.0.0
```

另外，可以更改端口，默认端口是 9200 ：

```shell
http.port: 9200
```

{% note warning %}
注意：这是指 http 端口，如果采用 REST API 对接 ES，那么就是采用的 http 协议
{% endnote%}

- 集群地址设置

配置集群的主机地址，配置之后集群的主机之间可以自动发现：

```shell
discovery.seed_hosts: ["192.168.3.43"]
```

> the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured

必须至少配置 `[discovery.seed_hosts，discovery.seed_providers，cluster.initial_master_nodes]` 中的一个。

```shell
$ egrep -v "^#|^$" config/elasticsearch.yml
cluster.name: michael-es
node.name: es-node-1
network.host: 0.0.0.0
http.port: 9200
discovery.seed_hosts: ["192.168.3.43"]
cluster.initial_master_nodes: ["es-node-1"]
```

经过上面的配置，这时候就可以使用 `http://192.168.3.43:9200/` 看到结果了。

### ES-FAQ

Q1：`[1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]`

```shell
echo "vm.max_map_count=262144" > /etc/sysctl.conf
sysctl -p
```

## Kibana

```shell
https://mirrors.huaweicloud.com/kibana/7.3.0/
wget https://mirrors.huaweicloud.com/kibana/7.3.0/kibana-7.3.0-linux-x86_64.tar.gz
wget https://mirrors.huaweicloud.com/kibana/7.3.0/kibana-7.3.0-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.3.0-linux-x86_64.tar.gz.sha512
```

## Filebeat

```shell
https://mirrors.huaweicloud.com/filebeat/7.3.0/
wget https://mirrors.huaweicloud.com/filebeat/7.3.0/filebeat-7.3.0-linux-x86_64.tar.gz
wget https://mirrors.huaweicloud.com/filebeat/7.3.0/filebeat-7.3.0-linux-x86_64.tar.gz.sha512
```

## 参考

- [程序羊-CentOS7上ElasticSearch安装填坑记](https://www.jianshu.com/p/04f4d7b4a1d3) FAQ 有帮助
