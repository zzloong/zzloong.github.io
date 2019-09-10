---
title: DevOps 之 ELK 基础设施搭建
date: 2019-08-14 22:10:57
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

![ELK](https://gitee.com/michael_xiang/images/raw/master/201909078-elk.png)

## Elasticsearch

官方的[Elasticsearch Reference](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html) 文档是相当全面了，而且，不同版本都可以切换，真是赞！如果英文的不想看，还提供了中文版的 [Elasticsearch: 权威指南](https://www.elastic.co/guide/cn/elasticsearch/guide/current/index.html)，虽然可能版本不是最新的，但是了解基本概念也是有帮助的。

Elasticsearch 包里自包含了 OpenJDK 的包，在 Elacticsearch 目录下的 `jdk` 目录中。如果你想要使用你自己配置好的 Java 版本，需要设置 `JAVA_HOME` 环境变量。

官方文档的 `Set up Elasticsearch` 下面有安装的指导，具体页面 [Installing Elasticsearch](https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html) 中提供了多种安装包对应的指导链接！本文就先选择 [tar 包](https://www.elastic.co/guide/en/elasticsearch/reference/current/targz.html)的方式安装。

由于本地机器有限，我们在同一台机器上模拟出 3 个节点，安装 ES 集群。

### 下载 ES 安装包

官网的下载地址简直是龟速，发现我司的[镜像站](https://mirrors.huaweicloud.com/elasticsearch/)上提供了安装包，试用后安利，速度不错：

```shell
wget https://mirrors.huaweicloud.com/elasticsearch/7.3.0/
wget https://mirrors.huaweicloud.com/elasticsearch/7.3.0/elasticsearch-7.3.0-linux-x86_64.tar.gz.sha512
# 验证安装包的完整性，如果没问题，会输出 OK
shasum -a 512 -c elasticsearch-7.3.0-linux-x86_64.tar.gz.sha512
tar -xzf elasticsearch-7.3.0-linux-x86_64.tar.gz
cp -R elasticsearch-7.3.0 es-node1
cp -R elasticsearch-7.3.0 es-node2
mv elasticsearch-7.3.0 es-node3
chown -R michael es-node* # 因为以 root 用户启动不了 ES
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

首先，我们先运行一个节点起来。

```shell
./bin/elasticsearch
```

{% note warning %}

不能使用 root 账号运行上面的命令，否则会报错：

```shell
Caused by: java.lang.RuntimeException: can not run elasticsearch as root
```

{% endnote %}

如果需要新建用户的话可以运行 `adduser michael`，修改密码：`passwd michael`，然后 `chown -R es-node*`。

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
node.name: node-1
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

### 安装插键

```shell
./bin/elasticsearch-plugin install analysis-icu
```

如果插键安装慢，可以先下载下来，再安装：

```shell
wget https://artifacts.elastic.co/downloads/elasticsearch-plugins/analysis-icu/analysis-icu-7.1.0.zip
./bin/elasticsearch-plugin install file://file path Of analysis-icu-7.1.0.zip
```

### 集群配置

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

### ES-FAQ

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

## Kibana

官方文档 [Installing Kibana](https://www.elastic.co/guide/en/kibana/current/install.html) 中提供了多种安装包对应的指导链接！本文就先选择 [tar 包](https://www.elastic.co/guide/en/kibana/current/targz.html)的方式安装。

### 下载 Kibana 安装包

同样，Kibana 在我司镜像站上也有对应的软件包：

```shell
https://mirrors.huaweicloud.com/kibana/7.3.0/
wget https://mirrors.huaweicloud.com/kibana/7.3.0/kibana-7.3.0-linux-x86_64.tar.gz
wget https://mirrors.huaweicloud.com/kibana/7.3.0/kibana-7.3.0-linux-x86_64.tar.gz.sha512
shasum -a 512 -c elasticsearch-7.3.0-linux-x86_64.tar.gz.sha512
tar xzf kibana-7.3.0-linux-x86_64.tar.gz
chown -R michael kibana-7.3.0-linux-x86_64
cd kibana-7.3.0-linux-x86_64
```

### 配置 Kibana

```shell
egrep -v "^#|^$" config/kibana.yml # 如下内容是修改的配置
server.port: 5601
server.host: "0.0.0.0"
elasticsearch.hosts: ["http://192.168.3.43:9200"]
kibana.index: ".kibana"
```

更多配置内容，可以阅读 [Configuring Kibana](https://www.elastic.co/guide/en/kibana/current/settings.html)

### 运行 Kibana

如下方式可以实现后台运行，避免 Ctrl+C 终止了程序：

```shell
nohup bin/kibana &
```

访问：`http://192.168.3.43:5601/`

这时候可以看到我们之前搭建的集群节点了：

![Kiana-ES](https://gitee.com/michael_xiang/images/raw/master/Dg7DMe.png)

## Filebeat && Logstash

```shell
https://mirrors.huaweicloud.com/filebeat/7.3.0/
wget https://mirrors.huaweicloud.com/filebeat/7.3.0/filebeat-7.3.0-linux-x86_64.tar.gz
wget https://mirrors.huaweicloud.com/filebeat/7.3.0/filebeat-7.3.0-linux-x86_64.tar.gz.sha512
```

目前项目中是采用的直接往 Elasticsearch 中存储数据 + Kibana 展示数据的方式。所以，[Filebeat](https://www.elastic.co/cn/products/beats/filebeat) 和 Logstash 暂时没接触过。不过，在 B 站上看到一位 UP 主发了很多相关的学习教程，安利一下：

- [EP26 - 安装与初始化配置ELK 6](https://www.bilibili.com/video/av23720863)
- [ELK搜索结果](https://ngx.hk/?s=elk) 上面这位 UP 主的博客

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

