---
title: 利用 Docker 安装 CICD 神器 Jenkins
date: 2019-06-04 23:55:08
tags:
  - cicd
  - DevOps
  - Jenkins
  - Docker
categories:
  - DevOps
keywords:
  - jenkins
  - docker
---

Jenkins 学习系列：

- [CentOS 安装 CICD 利器 Jenkins](https://michael728.github.io/2018/10/11/cicd-jenkins-01/)
- [利用 Docker 安装 CICD 神器 Jenkins](https://michael728.github.io/2019/06/04/cicd-docker-jenkins/)

本文主要介绍如何利用 Docker 来快速搭建 Jenkins，相比较以前的方式，方便快捷很多！

<!-- more -->

## 准备工作

由于是基于 Docker 安装 Jenkins，因此，你的环境上必须要安装好 Dokcer，并配置好加速器，不然下载镜像速度可能会比较慢。怎么安装，可以参考如下文章：

- [Linux--CentOS 安装 Docker 教程](https://michael728.github.io/2019/06/01/docker-centos-install/)
- [Docker 入门指南——常用命令](https://michael728.github.io/2019/06/02/docker-useful-often-commands/)
- [使用容器 Docker 创建开发环境](https://michael728.github.io/2019/06/02/docker-create-develop-environment/)

## Docker 命令运行 Jenkins

[jenkinsci/blueocean](https://hub.docker.com/r/jenkinsci/blueocean/tags) 是官方推荐的镜像版本，集成了流水线插键，推荐：

```shell
docker run \
  -u root \
  --name devops-jenkins \
  -d \
  -p 8080:8080 \
  -p 50000:50000 \
  -v /data/docker_local/jenkins-data:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkinsci/blueocean:1.15.1
```

参数说明：

- `-u root`：（可选）由于默认的 jenkins用户无权访问 `/var/run/docker.sock`，您需要以 root 身份运行 Jenkins 以允许 Jenkins 在你流水线上生成 docker 容器执行步骤。这仅影响在 Jenkins 主节点上的运行，如果你计划在你的流水线执行机上去执行，那这不是必须的。
- `jenkinsci/blueocean` 容器的端口 `8080` 到主机上的端口 `8080`。 第一个数字代表主机上的端口，而最后一个代表容器的端口。因此，如果您为此选项指定 `-p 49000:8080` ，您将通过端口主机端口 `49000` 访问 Jenkins

更多对参数的说明，访问[Installing Jenkins](https://jenkins.io/doc/book/installing/)

## Docker-Compose 运行 Jenkins

```shell
version: "3"
services:
    jenkins:
        image: jenkinsci/blueocean:1.15.1
        container_name: devops-jenkins # 容器名
        user: root
        restart: always
        ports:
            - "8080:8080"
            - "50000:50000"
        volumes:
            - "/data/docker_local/jenkins-data:/var/jenkins_home"
            - "/var/run/docker.sock:/var/run/docker.sock"
```

## 配置

### 配置插键代理

为了提高插键的下载速度，插键高级配置中，配置国内的代理：

- https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

有一个页面，专门查看 Jenkins 镜像状态的：

- [the status of Jenkins mirrors](http://mirrors.jenkins-ci.org/status.html)

## 常用命令

查看 Jenkins 的运行日志：

```shell
docker logs -f jenkins
```

## 归档

- [awesome-docker-service-for-me](https://github.com/Michael728/awesome-docker-service-for-me/tree/master/jenkins)

## 参考

- [官宣-安装Jenkins-中文](https://jenkins.io/zh/doc/book/installing/) 发现翻译的并不是特别好，推荐看[英文](https://jenkins.io/doc/book/installing/)
- [K8S牛刀小试CI之Jenkins on Docker篇(二)](https://www.imbajin.com/K8S%E7%89%9B%E5%88%80%E5%B0%8F%E8%AF%95CI%E4%B9%8BJenkins%E7%AF%87(%E4%BA%8C)/)