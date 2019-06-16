---
title: 使用 Docker Compose 方式搭建 SonarQube 服务
date: 2019-06-13 22:37:10
categories: DevOps
tags:
  - Docker
  - 代码质量
  - DevOps
  - CICD
keywords:
  - 代码质量
---

## 前言

在 CICD 流水线中，代码质量其实是很关键的一环。[SonarQube](https://www.sonarqube.org/) 是一个「持续代码质量」检测的服务。在 DevOps 持续集成中对把控代码质量很有帮助。

![](https://ws1.sinaimg.cn/mw690/6d9475f6gy1g416hyo9c0j20x40c1407.jpg)

<!-- more -->

## 命令行启动

首先以命令行的方式启动，将默认的一些配置拷贝到本地磁盘，比如插键、配置文件等：

```shell
mkdir -p /data/sonar
docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 sonarqube:7.7-community
docker cp sonarqube:/opt/sonarqube/conf /data/sonar
docker cp sonarqube:/opt/sonarqube/extensions /data/sonar
wget -P /data/sonar/extensions/plugins https://github.com/SonarQubeCommunity/sonar-l10n-zh/releases/download/sonar-l10n-zh-plugin-1.27/sonar-l10n-zh-plugin-1.27.jar
docker stop sonarqube
docker rm sonarqube
```

为了对 sonarqube 进行汉化，下载了中文插键。更多的插键下载，可以访问：[插键](https://docs.sonarqube.org/display/PLUG/Plugin+Library)

## 配置数据库

我使用的是 MySQL 数据库，为了实现权限分离，创建了单独的 sonar 数据库和对应的账号：

```shell
#docker exec -it devops-mysql
#mysql -u root -p
mysql> CREATE DATABASE sonar CHARACTER SET utf8 COLLATE utf8_general_ci;
mysql> CREATE USER 'sonar' IDENTIFIED BY 'sonar';
mysql> GRANT ALL ON sonar.* TO 'sonar'@'%' IDENTIFIED BY 'sonar';
mysql> GRANT ALL ON sonar.* TO 'sonar'@'localhost' IDENTIFIED BY 'sonar';
mysql> FLUSH PRIVILEGES;
```

数据库我也是用 docker 创建的，可以参考之前的文章[使用容器 Docker 创建开发环境](https://michael728.github.io/2019/06/02/docker-create-develop-environment/)。

## 连接数据库

```shell
docker run -d --name sonarqube \
    -p 9000:9000 \
    -e sonar.jdbc.username=root \
    -e sonar.jdbc.password=123456 \
    -e sonar.jdbc.url="jdbc:mysql://192.168.3.43:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false" \
    -v /data/sonar/extensions:/opt/sonarqube/extensions \
    sonarqube:7.7-community
```

> Use of the environment variables SONARQUBE_JDBC_USERNAME, SONARQUBE_JDBC_PASSWORD and SONARQUBE_JDBC_URL is deprecated, and will stop working in future releases.

网上一些教程还是采用的旧的环境变量，官网已经说快要废弃了。

## Compose 方式启动

如果你 MySQL 和 sonarqube 服务是在一个 docker-compose.yml 文件中的话，那么它们默认会处于同一个容器网络中，sonarqube 是可以连接上的。对这块网络知识不了解的，可以查看这个总结：[Docker Compose 方式下的容器网络基础知识点](https://michael728.github.io/2019/06/15/docker-compose-networks/)

如果是在同一台机器上，MySQL 容器已经创建好了，和 sonarqube 不是同一个 docker-compose.yml 方式创建的，那么，这时候就需要配置，才能让两个容器实现网络互通。

sonarqube 的 docker-compose.yml：

```shell
version: "2.4"
services:
    sonar:
        image: sonarqube:7.7-community
        container_name: devops-sonar
        ports:
            - "9000:9000"
            - "9002:9002"
        volumes:
            - "/data/sonar/conf:/opt/sonarqube/conf"
            - "/data/sonar/extensions:/opt/sonarqube/extensions"
            - "/data/sonar/logs:/opt/sonarqube/logs"
            - "/data/sonar/data:/opt/sonarqube/data"
        environment:
          sonar.jdbc.username: sonar #root管理员用户密码
          sonar.jdbc.password: sonar   #创建test用户
          sonar.jdbc.url: "jdbc:mysql://devops-mysql:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false"
        restart: always
        networks:
         - db_net

networks:
  db_net:
    external: true
```

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g4165ysbgkj21gc0y27fl.jpg)

可以发现，这时候 sonarqube 网络和 MySQL 网络是通的，而且，使用 `service name` 或者 `container name` 都可以查询到主机。因此，`sonar.jdbc.url` 可以配置成：

- `jdbc:mysql://mysql:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false`
- `jdbc:mysql://devops-mysql:3306/sonar?useUnicode=true&characterEncoding=utf8&rewriteBatchedStatements=true&useConfigs=maxPerformance&useSSL=false`

我们的服务已经正常启动啦：

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g416ltc7dfj21020hlwgf.jpg)

## 示例代码

本文的 `docker-compose` 文件，归档在：[awesome-docker-service-for-me](https://github.com/Michael728/awesome-docker-service-for-me)
