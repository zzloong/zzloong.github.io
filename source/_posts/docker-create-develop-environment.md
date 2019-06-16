---
title: 使用容器 Docker 创建开发环境
date: 2019-06-02 15:41:19
categories: 容器
tags:
 - docker
 - Env
keywords:
 - 容器
---

![](https://ws1.sinaimg.cn/large/6d9475f6ly1g3n9oufsy9j20hs0b4myy.jpg)

Docker 容器学习笔记系列：

- [Linux--CentOS 安装 Docker 教程](https://michael728.github.io/2019/06/01/docker-centos-install/)
- [Docker 入门指南——常用命令](https://michael728.github.io/2019/06/02/docker-useful-often-commands/)
- [使用容器 Docker 创建开发环境](https://michael728.github.io/2019/06/02/docker-create-develop-environment/)

个人在开发时能方便快捷的搭建对应的测试环境，比如搭建一个 Redis 数据库、MongoDB 数据库、Elasticsearch 服务。这样就不用拿公共的基础设施做测试了，避免损坏数据、破坏了线上环境。

本文就介绍如何利用容器 Docker 来快速搭建开发测试环境。

<!-- more -->

## MySQL

### 简单创建

```shell
docker run -d \
--name mysql \
-p 3306:3306 \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:5.7
```

参数说明：

- `-d`：以后台方式运行
- `--name`：将运行的容器命名为 `mysql`
- `-p`：端口映射，`host_ip:container_ip`，将主机的 3306 端口映射为 容器内部的 3306 端口，数据库连接时，是连接的你的 `host_ip`，这二者的顺序不要搞混
- `-e`：设置环境变量，指定 `root` 账号的密码为 `123456`

上面这种方式运行后，我们将容器中的 mysql 数据和配置文件拷贝到宿主机上。（获取原始配置文件）。

因为没有将宿主机和容器相关目录挂载，这样会导致容器如果被删除，数据会丢失。

```shell
# 创建好目录，不然会报错
mkdir -p /dada/docker_local/mysql

# 将容器中的 mysql 配置文件复制到宿主机中指定路径下，路径你可以根据需要，自行修改
docker cp mysql:/etc/mysql/mysql.conf.d/mysqld.cnf /data/docker_local/mysql/config

# 将容器中的 mysql 存储目录复制到宿主机中
docker cp mysql:/var/lib/mysql/ /data/docker_local/mysql/data
```

这里放一份默认的配置文件 `mysqld.cnf`：
```shell
[mysqld]
pid-file        = /var/run/mysqld/mysqld.pid
socket          = /var/run/mysqld/mysqld.sock
datadir         = /var/lib/mysql
#log-error      = /var/log/mysql/error.log
# By default we only accept connections from localhost
#bind-address   = 127.0.0.1
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
```

完成上面的操作之后，将已经运行的容器删除：
```shell
docker rm mysql
```

### 正式创建

接下来正式运行 MySQL 容器：
```shell
docker run -d \
--name mysql \
-p 3306:3306 \
-v /data/docker_local/mysql/config/mysqld.cnf:/etc/mysql/mysql.conf.d/mysqld.cnf \
-v /data/docker_local/mysql/data/mysql:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
mysql:5.7
```

- `-v`  将宿主机中的文件挂载到将容器中，容器中产生的数据会持久化下来，语法：`-v [host-src:]container-dest[:<options>]`

其实，如果你一开始就有了默认的配置文件，那么，就可以跳过之前的步骤，直接运行正式的创建 MySQL 容器的命令。

## MongoDB

### 创建容器

```shell
# 创建持久化目录
mkdir -p /data/docker_local/mongo/configdb
mkdir -p /data/docker_local/mongo/data/db

docker run -d \
--name mongo \
-v /data/docker_local/mongo/configdb:/data/configdb \
-v /data/docker_local/mongo/data/db:/data/db \
-p 27017:27017 \
mongo:4 \
--auth
```

启动参数含义在 MySQL 启动时已经见过了，除了 `auth`：
- `--auth`：开启权限验证模式。默认情况下，mongo 数据库没有添加认证约束，为了增强数据库的安全性，我们需要对数据库添加授权认证。当我不加这个认证约束时，一个数据库的账号可以操作另一个数据库的数据，只有加了这个参数，我们针对某些数据库设置的角色，才仅在这个数据库上生效。

### 添加管理员账号

- 进入容器
```
docker exec -it mongo mongo admin
```

进入容器 `mongo`，运行命令 `mongo admin`，实现了：
- 进入 mongo 命令行
- 切换为 admin 数据库

创建一个拥有最高权限的 root 账号：
```
db.createUser({ user: 'admin', pwd: '123456', roles: [ { role: "root", db: "admin" } ] });
```

role 角色参数：
- `Read`：允许用户读取指定数据库
- `readWrite`：允许用户读写指定数据库
- `dbAdmin`：允许用户在指定数据库中执行管理函数，如索引创建、删除，查看统计或访问system.profile
- `userAdmin`：允许用户向system.users集合写入，可以找指定数据库里创建、删除和管理用户
- `clusterAdmin`：只在admin数据库中可用，赋予用户所有分片和复制集相关函数的管理权限
- `readAnyDatabase`：只在admin数据库中可用，赋予用户所有数据库的读权限
- `readWriteAnyDatabase`：只在admin数据库中可用，赋予用户所有数据库的读写权限
- `userAdminAnyDatabase`：只在admin数据库中可用，赋予用户所有数据库的userAdmin权限
- `dbAdminAnyDatabase`：只在admin数据库中可用，赋予用户所有数据库的dbAdmin权限
- `root`：只在admin数据库中可用。超级账号，超级权限

### 创建访问指定数据库的用户

管理员已经创建成功后，我们需要重新连接 mongo 数据库，用管理员进行登录，并为目标数据库创建目标用户

```shell
# 进入容器，并切换到 admin 数据库
docker exec -it mongo mongo admin
# 授权 admin
db.auth("admin", "123456");
# 创建访问指定数据库的用户
use beta;
db.createUser({ user: 'test', pwd:'test', roles: [ {role:"readWrite",db:"beta"}]});
db.auth("test","test");
# 尝试插入一条数据
use beta
db.test.insertOne({name:"michael",age:"28"})
# 搜索 test collection 全部记录
db.test.find()
```

test 用户可以对(也只能对) beta 库进行操作

补充：
- 查看容器运行日志：`docker logs mysql`

## docker-compose

`docker-compse` 俗称 Docker 三剑客，这里试试用它来同时启动 MySQL、MongoDB 吧：

```shell
version: "2"
services:
    mongodb:
        image: mongo:4
        container_name: mongodb # 容器名
        ports:
            - "27017:27017"
        volumes:
            - "/data/docker_local/mongo/configdb:/data/configdb"
            - "/data/docker_local/mongo/data/db:/data/db"
        command: --auth # 开启授权验证
    mysql:
        image: mysql:5.7
        container_name: mysqldb # 容器名
        ports:
            - "3306:3306"
        command: mysqld --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci #设置utf8字符集
        restart: always
        environment:
          MYSQL_ROOT_PASSWORD: 123456 #root管理员用户密码
          MYSQL_USER: test   #创建test用户
          MYSQL_PASSWORD: test  #设置test用户的密码
        volumes:
            - "/data/docker_local/mysql/config/mysqld.cnf:/etc/mysql/mysql.conf.d/mysqld.cnf"
            - "/data/docker_local/mysql/data/mysql:/var/lib/mysql"
```

进入到上面编写的docker-compose.yml文件的目录，运行命令：

```shell
# 启动所有服务
docker-compose up -d
# 单启动 mysql
docker-compose up -d mysql
# 暂停 mysql
docker-compose stop mysql
# 重新启动容器
docker-compose restart mysql
# 登录到容器中
docker-compose exec mysql bash
# 删除所有容器和镜像
docker-compose down
# 显示所有容器
docker-compose ps
# 查看mysql的日志
docker-compose logs mysql
# 查看mysql的实时日志
docker-compose logs -f mysql
```

## nginx

使用 Nginx 容器搭建文件服务区

file.conf：

```shell
server {
        client_max_body_size 4G;
        listen  8889;  ## listen for ipv4; this line is default and implied
        server_name <host_ip or domain_name>; # 换成你服务器的 IP 或绑定的域名
        charset utf-8;
        root /data;
        location / {
            autoindex on; # 要想设置nginx的目录浏览功能，必须要打开下面这个参数
            autoindex_exact_size off; # 默认是开，以b为单位，关闭后，会显示MB GB
            autoindex_localtime on;
        }
}
```

docker-compose.yml：

```shell
version: "2"
services:
    nginx:
        image: nginx
        container_name: nginx # 容器名
        ports:
            - "8889:8889"
        volumes:
            - "/data/nginx/conf.d:/etc/nginx/conf.d"
            - "/data/nginx/data:/data"
        restart: always
```

为了避免中文乱码，需要配置 `charset utf-8;`

### 怎么给文件服务器设置访问密码呢？只需要修改一下配置即可：

```shell
server {
        client_max_body_size 4G;
        listen  8889;  ## listen for ipv4; this line is default and implied
        server_name 207.148.104.42;
        charset utf-8;
        root /data;
        location / {
            auth_basic "Restricted";
            auth_basic_user_file /data/pass_file;
            autoindex on;
            autoindex_exact_size off;
            autoindex_localtime on;
        }
}
```

生成用户名和密码：

```shell
htpasswd -c -d pass_file michael
```

- `htpasswd` 命令没有的话，需要：`yum install httpd-tools -y` 安装；
- `-c` 创建一个文件；
- `-d` 强制 CRYPT 加密密码（最多8个字符，不安全）
- `-s` Force SHA encryption of the password (insecure)

参考：[Nginx配置静态文件服务](https://blog.csdn.net/dutsoft/article/details/55004064)

### 控制访问 nginx 的 IP

网段的写法是这样的：`192.168.1.0/24` 这样的形式。

```shell
deny 192.168.1.11;
deny 192.168.1.123;
deny 192.168.1.0/24; # 屏蔽整个192.168.1.* C段IP
```

允许某几个 IP 能访问，其他 IP 均不能访问：

```shell
allow 192.168.1.1;
allow 192.168.1.2;
deny all;
```

示例：

```shell
server {
        client_max_body_size 4G;
        listen  8889;  ## listen for ipv4; this line is default and implied
        server_name 207.148.104.42;
        charset utf-8;
        root /data;
        location / {
            allow 112.10.84.226;
            deny all;
            auth_basic "Restricted";
            auth_basic_user_file /data/pass_file;
            autoindex on;
            autoindex_exact_size off;
            autoindex_localtime on;
        }
}
```

- [[原创]nginx如何禁止指定IP或IP段访问](http://www.21yunwei.com/archives/2540)
- [Nginx基础篇（4）- Nginx请求限制和访问控制](https://segmentfault.com/a/1190000015591117)

## FAQ

### ifconfig command not found 解决办法

```shell
yum install net-tools -y
```

## 本文示例

- [Github-awesome-docker-service-for-me](https://github.com/Michael728/awesome-docker-service-for-me/tree/master/)

## 参考

- [使用Docker快速搭建各种测试环境](https://bestzuo.cn/posts/3822108690.html) 本文主要参考
- [Docker初步实践](http://luoxia.me/code/2017/04/01/docker%E5%88%9D%E6%AD%A5%E5%AE%9E%E8%B7%B5/)
- [CSDN-docker安装mongo及开启用户认证](https://blog.csdn.net/yori_chen/article/details/81036149)
- [docker-compose 安装mysql5.7](http://www.spoofer.top/2017/08/07/docker-compose%E5%AE%89%E8%A3%85mysql5.7)
- [使用 Docker Compose 搭建 MySQL 数据库主从复制实例](https://juejin.im/post/5a2e4bd66fb9a044fa19cfb7)
