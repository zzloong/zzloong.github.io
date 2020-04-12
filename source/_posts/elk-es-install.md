---
title: Elasticsearch 安装及集群的配置
date: 2020-04-12 23:10:57
categories: ELK
tags:
    - ELK
    - Search
keywords:
  - Elasticsearch
  - Kibana
  - Logstash
  - Filebeat
---

## 简介

![ELK](https://gitee.com/michael_xiang/images/raw/master/201909078-elk.png)

## Elasticsearch/ES

官方的[Elasticsearch Reference](https://www.elastic.co/guide/en/elasticsearch/reference/index.html) 提供了不同版本的文档连接，真是赞！

> 如果英文的不想看，还提供了中文版的 [Elasticsearch 2.x: 权威指南](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)，版本不是最新的，但是了解基本概念也是有帮助的。

Elasticsearch 7.x 包里自包含了 OpenJDK 的包。如果你想要使用你自己配置好的 Java 版本，需要设置 `JAVA_HOME` 环境变量 —— [参考](https://www.elastic.co/guide/en/elasticsearch/reference/current/setup.html)

官方文档 `Set up Elasticsearch` 有各个 OS 的安装指导，页面 [Installing Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/7.3/install-elasticsearch.html) 中提供了多种安装包对应的指导链接！

本文选择绿色安装包的的方式（`tar.gz`）安装。

> 由于实验机器有限，可以在同一台机器上模拟出 3 个节点，安装 ES 集群。

## 安装 ES

### 准备工作

{% note info %}

不能使用 root 用户启动 es，否则会报错：

```shell
Caused by: java.lang.RuntimeException: can not run elasticsearch as root
```

{% endnote %}

如果需要新建用户的话可以运行 `sudo adduser es`，修改 es 用户的密码：`sudo passwd es`。

### 下载 ES 安装包

安装包下载地址：
- [官方-Past Releases](https://www.elastic.co/cn/downloads/past-releases#elasticsearch) 官网的下载地址简直是龟速
- [华为镜像站](https://mirrors.huaweicloud.com/elasticsearch/) 下载速度不错，推荐

下面的步骤参考 [Set up Elasticsearch » Installing Elasticsearch » Install Elasticsearch from archive on Linux or MacOS](https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html)

```shell
# 下载安装包
wget https://mirrors.huaweicloud.com/elasticsearch/7.3.0/elasticsearch-7.3.0-linux-x86_64.tar.gz
wget https://mirrors.huaweicloud.com/elasticsearch/7.3.0/elasticsearch-7.3.0-linux-x86_64.tar.gz.sha512
# 验证安装包的完整性，如果没问题，会输出 OK
shasum -a 512 -c elasticsearch-7.3.0-linux-x86_64.tar.gz.sha512
tar -xzf elasticsearch-7.3.0-linux-x86_64.tar.gz
# 将目录复制三份，作为三个节点
cp -R elasticsearch-7.3.0 es-node1
cp -R elasticsearch-7.3.0 es-node2
mv elasticsearch-7.3.0 es-node3
# 因为以 root 用户启动不了 ES
chown -R es es-node*
```

{% note warning %}
如果是 Mac 平台，则下载包 elasticsearch-{version}-darwin-x86_64.tar.gz。

macOS Catalina 在你第一次运行 es 时，会弹出对话框阻止运行，你需要到设置-》安全隐私中允许才行。为了阻止这种告警，可以运行如下的命令：

```
xattr -d -r com.apple.quarantine <archive-or-directory>
```
{% endnote %}

- `$ES_HOME` 是指这里 tar 包解压后的文件夹目录

解压后的[目录组成](https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html#targz-layout)：

```shell
.
├── bin # 二进制脚本存放目录，包括 elasticsearch 来指定运行一个 node，包括 elasticsearch-plugin 来安装 plugins
├── config # 包含了 elasticsearch.yml 配置文件
├── data # 节点上分配的每个 index/分片 的数据文件
├── lib
├── LICENSE.txt
├── logs
├── modules
├── NOTICE.txt
├── plugins # 插键文件存放的位置
└── README.textile
```

### 使用命令行运行 Elasticsearch

首先，我们先运行一个节点起来。

```shell
./bin/elasticsearch
```

### 检查一下运行状态

```shell
curl -X GET "localhost:9200/?pretty"
```

或者在浏览器中访问 `localhost:9200` 都可以，会返回：

```shell
{
    "name": "node-1",
    "cluster_name": "michael-es",
    "cluster_uuid": "GlzI_v__QJ2s9ewAgomOqg",
    "version": {
        "number": "7.3.0",
        "build_flavor": "default",
        "build_type": "tar",
        "build_hash": "de777fa",
        "build_date": "2019-07-24T18:30:11.767338Z",
        "build_snapshot": false,
        "lucene_version": "8.1.0",
        "minimum_wire_compatibility_version": "6.8.0",
        "minimum_index_compatibility_version": "6.0.0-beta1"
    },
    "tagline": "You Know, for Search"
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

## 配置 ES

官网关于配置的内容主要有两处：
- [Configuraing Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/settings.html)
- [Important Elasticsearch configuration](https://www.elastic.co/guide/en/elasticsearch/reference/current/important-settings.html)

Elasticsearch 主要有三个配置文件：
- `elasticsearch.yml` ES 的配置，[more](https://www.elastic.co/guide/en/elasticsearch/reference/current/important-settings.html)
- `jvm.options` ES JVM 配置，[more](https://www.elastic.co/guide/en/elasticsearch/reference/current/jvm-options.html#jvm-options)
- `log4j2.properties` ES 日志配置，[more](https://www.elastic.co/guide/en/elasticsearch/reference/current/logging.html#logging)

> 配置文件主要位于 `$ES_HOME/config` 目录下，也可以通过 `ES_PATH_CONF` 环境变量来修改

YAML 的配置形式参考：
```yaml
path:
    data: /var/lib/elasticsearch
    logs: /var/log/elasticsearch
```

设置也可以按如下方式展平：
```yaml
path.data: /var/lib/elasticsearch
path.logs: /var/log/elasticsearch
```

### 使用命令行配置 ES

ES 默认会加载位于 `$ES_HOME/config/elasticsearch.yml` 的配置文件。

任何能够通过配置文件设置的内容，都可以通过命令行使用 `-E` 的语法进行指定：

```shell
./bin/elasticsearch -d -Ecluster.name=my_cluster -Enode.name=node_1
```

{% note info %}
通常，任何群集范围的设置（如 cluster.name）都应添加到 `elasticsearch.yml` 配置文件中，而任何特定的节点设置（如 node.name）都可以在命令行中指定
{% endnote %}

### JVM 配置

[JVM 参数设置](https://www.elastic.co/guide/en/elasticsearch/reference/current/jvm-options.html#jvm-options)可以通过 `jvm.options` 文件（推荐方式）或者 `ES_JAVA_OPTS` 环境变量来修改。

`jvm.options` 位于 
- `config/jvm.options` 当通过 `tar` or `zip` 包安装
- `/etc/elasticsearch/jvm.options` 当通过 Debian or RPM packages

官网也介绍了如何[设置堆大小](https://www.elastic.co/guide/en/elasticsearch/reference/current/heap-size.html)。

默认情况，ES 告诉 JVM 使用一个最小和最大都为 1GB 的堆。但是到了生产环境，这个配置就比较重要了，确保 ES 有足够堆空间可用。

ES 使用 `Xms(minimum heap size)` 和 `Xmx(maxmimum heap size)` 设置堆大小。你应该将这两个值设为同样的大小。

- `Xms` 和 `Xmx` 不能大于你物理机内存的 50%。

设置的示例：
```
-Xms2g 
-Xmx2g
```

### cluster.name

`cluster.name` 设置[集群名称](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster.name.html)。一个节点只能加入一个集群中，默认的集群名称是 `elasticsearch`.

```shell
cluster.name: michael-es
```

{% note info %}
确保节点的集群名称要设置正确，这样才能加入到同一个集群中
{% endnote %}

### node.name

`node.name` 可以配置每个[节点的名称](https://www.elastic.co/guide/en/elasticsearch/reference/current/node.name.html)。用来提供可读性高的 ES 实例名称，默认名称是机器的 `hostname`，可以自定义：

```shell
node.name: node-1
```

> 集群中每个节点的名称都不要相同

### network.host

`network.host` 设置访问的[地址](https://www.elastic.co/guide/en/elasticsearch/reference/current/network.host.html)。我们需要设定 ES 运行绑定的 Host，默认是无法公开访问的，默认是回环地址 `127.0.0.1`。如果要和其他节点形成集群，建议设置为主机的公网 IP 或 `0.0.0.0`：

```shell
network.host: 0.0.0.0
```

> 更多的网络设置可以阅读 [Network Settings](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-network.html)

### http.port

`http.port` ，默认端口是 9200 ：

```shell
http.port: 9200
```

{% note warning %}
注意：这是指 http 端口，如果采用 REST API 对接 ES，那么就是采用的 http 协议
{% endnote%}

### Important discovery and cluster formation settings

有两种重要的发现和集群形成配置，以便集群中的节点能够彼此发现和选择一个主节点。[Important discovery and cluster formation settings](https://www.elastic.co/guide/en/elasticsearch/reference/current/discovery-settings.html)

- `discovery.seed_hosts`

开箱即用，无需任何网络配置， ES 将绑定到可用的环回地址，并将扫描本地端口 9300 - 9305，以尝试连接到同一服务器上运行的其他节点。 这无需任何配置即可提供自动群集的体验。

如果要与其他主机上的节点组成集群，你应使用 `discovery.seed_hosts` 设置，来提供集群中其他主机的列表，这些主机符合主机要求并且可能处于活动状态且可达，以便寻址[发现过程](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-hosts-providers.html)。 此设置应该是群集中所有符合主机资格的节点的地址的列表。 每个地址可以是IP地址，也可以是通过DNS解析为一个或多个IP地址的主机名（`hostname`）。

配置集群的主机地址，配置之后集群的主机之间可以自动发现（可以带上端口，例如 `192.168.1.10:9200`）：

```shell
discovery.seed_hosts: ["192.168.3.43"]
```

> the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured

必须至少配置 `[discovery.seed_hosts，discovery.seed_providers，cluster.initial_master_nodes]` 中的一个。

- `cluster.initial_master_nodes`

首次启动全新的 ES 集群时，会出现一个[集群引导/cluster bootstrapping](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-bootstrap-cluster.html)步骤，该步骤确定了在第一次选举中的主节点的集合。 在开发模式下，没有进行发现设置，此步骤由节点本身自动执行。 由于这种自动引导从本质上讲是[不安全的](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-quorums.html)，因此当您在[生产模式](https://www.elastic.co/guide/en/elasticsearch/reference/current/bootstrap-checks.html#dev-vs-prod-mode)下第一次启动全新的群集时，你必须显式列出符合资格的主机节点。使用 `cluster.initial_master_nodes` 设置来设置此列表。**重新启动集群或将新节点添加到现有集群时，不应使用此设置**

初始主节点应通过其 `node.name` 标识，默认为其主机名。 确保 `cluster.initial_master_nodes` 中的值与 `node.name` 完全匹配

```shell
$ egrep -v "^#|^$" config/elasticsearch.yml
cluster.name: michael-es
node.name: node-1
network.host: 0.0.0.0
http.port: 9200
discovery.seed_hosts: ["192.168.3.43"]
cluster.initial_master_nodes: ["node-1"]
```

经过上面的配置，这时候就可以使用 `http://192.168.3.43:9200/` 看到结果了。

关于 cluster.initial_master_nodes 可以查看如下资料

- [Bootstrapping a cluster](https://www.elastic.co/guide/en/elasticsearch/reference/master/modules-discovery-bootstrap-cluster.html)
- [Discovery and cluster formation settings](https://www.elastic.co/guide/en/elasticsearch/reference/current/modules-discovery-settings.html)

## 集群配置

分别进入对应 es-node2 和 es-node3 的文件夹，设置如下：

```shell
# es-node2
cluster.name: michael-es
node.name: node-2
network.host: 0.0.0.0
discovery.seed_hosts: ["192.168.3.43"]

# es-node3
cluster.name: michael-es
node.name: node-3
network.host: 0.0.0.0
discovery.seed_hosts: ["192.168.3.43"]
```

我们通过访问 `http://192.168.3.43:9200/_cat/nodes`查看集群是否 OK：

```shell
172.20.0.1 18 98 35 1.16 0.69 0.58 dim - node-2
172.20.0.1 14 98 35 1.16 0.69 0.58 dim - node-3
172.20.0.1 19 98 35 1.16 0.69 0.58 dim * node-1
```

有没有发现，我并没有给 `es-node2` 和 `es-node3` 明确的指定端口，为什么在一台机器上也成功启动了这两个节点？因为 Elasticsearch 会取用 9200~9299 这个范围内的端口，如果 9200 被占用，就选择 9201，依次类推。

其实，还有一个跟简单的方法创建集群，我们首先将上面运行的三个节点停止掉，然后进入 es-node1 文件夹下：

```shell
mkdir -p data/data{1,2,3}
./bin/elasticsearch -E node.name=node-1 -E cluster.name=michael-es -E path.data=data/data1 -E path.logs=logs/logs1 -d -p pid1
./bin/elasticsearch -E node.name=node-2 -E cluster.name=michael-es -E path.data=data/data2 -E path.logs=logs/logs2 -E http.port=9201 -d -p pid2
./bin/elasticsearch -E node.name=node-3 -E cluster.name=michael-es -E path.data=data/data3 -E path.logs=logs/logs3 -E http.port=9202 -d -p pid3
```

## 安装插键

```shell
./bin/elasticsearch-plugin install analysis-icu
```

如果插键安装慢，可以先下载下来，再安装：

```shell
wget https://artifacts.elastic.co/downloads/elasticsearch-plugins/analysis-icu/analysis-icu-7.3.0.zip
./bin/elasticsearch-plugin install file://file path Of analysis-icu-7.1.0.zip
```

## ES-FAQ

Q1：`[1]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]`

```shell
echo "vm.max_map_count=262144" > /etc/sysctl.conf
sysctl -p
```

Q2：`max file descriptors [4096] for elasticsearch process is too low, increase to at least [65536]`

```shell
sudo vim /etc/security/limits.conf
# 加入以下内容
* soft nofile 300000
* hard nofile 300000
* soft nproc 102400
* soft memlock unlimited
* hard memlock unlimited
```

Q3：`master_not_discovered_exception`

主节点指定的名字要保证存在，别指定了不存在的节点名。

## 总结

本文是通过 tar 包方式安装的，发现，还不如用 RPM 包来的方便。不过配置的内容其实差不多，区别可能就是，RPM 包方式，可以直接用 systemctl 的命令查看状态、重启等。

## 参考

- [程序羊-CentOS7上ElasticSearch安装填坑记](https://www.jianshu.com/p/04f4d7b4a1d3) FAQ 有帮助
- [极客时间-Elasticsearch核心技术与实战](https://time.geekbang.org/course/detail/197-102661) 这篇文章阐述了 ES 集群的主节点的仲裁等知识
- [搭建ELFK日志采集系统](https://jeremy-xu.oschina.io/2018/10/%E6%90%AD%E5%BB%BAelfk%E6%97%A5%E5%BF%97%E9%87%87%E9%9B%86%E7%B3%BB%E7%BB%9F/)
- [静觅—Ubuntu 搭建 Elasticsearch 6 集群流程](https://cuiqingcai.com/6255.html)
- [阮一峰-全文搜索引擎 Elasticsearch 入门教程](https://www.ruanyifeng.com/blog/2017/08/elasticsearch.html)
- [ELK 架构之 Elasticsearch 和 Kibana 安装配置](https://www.cnblogs.com/xishuai/p/elk-elasticsearch-kibana.html)
- [使用 ELK(Elasticsearch + Logstash + Kibana) 搭建日志集中分析平台实践](https://wsgzao.github.io/post/elk/)
- [手把手教你，在CentOS上安装ELK，进行服务器日志收集](http://www.justdojava.com/2019/08/11/elk-install/)

Filebeat
- [博客园-开始使用Filebeat](https://www.cnblogs.com/cjsblog/p/9445792.html)

SpringBoot+ES
- [【ES】Java High Level REST Client 使用示例（增加修改）](https://my.oschina.net/zhaixingzu/blog/3010467)
- [Elasticsearch Java Rest Client 上手指南（上）](https://www.jianshu.com/p/c1f2161a5d22)
- [博客园——springboot elasticsearch 集成注意事项](https://www.cnblogs.com/guozp/archive/2018/04/02/8686904.html)
- [lasticsearch系列七：ES Java客户端-Elasticsearch Java client（ES Client 简介、Java REST Client、Java Client、Spring Data Elasticsearch）](https://www.cnblogs.com/leeSmall/p/9218779.html)
