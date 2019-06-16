---
title: Docker Compose 方式下的容器网络基础知识点
date: 2019-06-15 00:17:12
categories: 容器
tags:
  - Docker
  - network
keywords:
  - 容器
  - 通信
  - 网络
---

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g415cdupwjj20fa0faq3q.jpg)

## 前言

本周使用 [Docker Compose 方式搭建了 sonarqube 的服务](https://michael728.github.io/2019/06/13/docker-compose-install-sonarqube/)，并进行了总结。遇到的问题：

<!-- more -->

我在本地的机器上，一开始用 Compose 的方式创建了一个 MySQL 的容器了。然后在再利用另一份 `docker-compose.yml` 文件创建 sonarqube 容器时，尝试配置 MySQL 替代 sonarqube 默认采用的 H2 数据库。但是 sonarqube 却怎么也无法连接上 MySQL。

为了解决这个网络不通的问题，需要了解一下容器间网络通信的基础。





## networks

关于 Compose 的网络，官网有专门的一节进行介绍。查看[Networking in Compose](https://docs.docker.com/compose/networking/)

> By default Compose sets up a single network for your app. Each container for a service joins the default network and is both reachable by other containers on that network, and discoverable by them at a hostname identical to the container name.

Compose 默认给你的 app 设置一个网络。 service 中的每个容器默认都加入这个网络，容器之间彼此是互通的。并且，可以利用容器名字识别到。

{% note warning no-icon %}
Note: 你 app 的网络默认情况下是和你的 `project name` 有关的。这个 `project name` 其实就是你 docker-compose.yml 文件存放的那个目录的名字。比如，目录名叫 `db`，那么默认情况下会创建一个叫 `db_default` 的网络。你可以使用 `--project-name` 或 `COMPSE_PROJECT_NAME` 环境变量。
{% endnote %}

举个栗子,`docker-compose.yml`：

```shell
version: "3"
services:
  web:
    build: .
    ports:
      - "8000:8000"
  db:
    image: postgres
    ports:
      - "8001:5432"
```

假设，上面这个文件放在 `myapp` 文件夹下。那么，当你 `docker-compose up -d` 之后：

- 一个叫 `myapp_default` 网络被创建；
- 一个使用 web 配置的容器会被创建，它以 `web` 的名字加入了 `myapp_default` 这个网络；
- 一个使用 db 配置的容器会被创建，它以 `db` 的名字加入了 `myapp_default` 网络；

> Each container can now look up the hostname web or db and get back the appropriate container’s IP address

现在，每个容器都可以查找主机名 `web` 或 `db`，并获取相应容器的 IP 地址。例如，`web` 应用的代码可以使用 URL `postgres://db:5432` 连接数据库并使用它。

重要的是要注意 `HOST_PORT` 和 `CONTAINER_PORT` 之间的区别。前者是指的是宿主机的端口，后者指的是容器中的端口。容器网络中的服务间使用的是 `CONTAINER_PORT` 通信。`HOST_PORT` 定义了是为了容器网络外被调用的。所以，前面才使用的是 `postgres://db:5432` 而不是 `postgres://db:8001`。因为他们属于同一个容器网络中。

在官网的 [Compose file version 2 reference](https://docs.docker.com/compose/compose-file/compose-file-v2/#networks) 一段中，也有相关网络介绍。

{% note success no-icon %}
Networks to join, referencing entries under the top-level networks key.
{% endnote %}

这句话需要注意到，`services` 下级的服务中 `networks` 指定的网络不是指要创建的网络，而是这个服务要加入的网络。

所以说，这时候如果你指定了一个没有的网络，就会报错啦，类似这种：

```shell
ERROR: Service "mongodb" uses an undefined network "mogo_net"
```

需要定义才能加入。经过试验，我在 Compose 文件中，使用 top-level 的 `networks` 定义一下网络，运行时，会自动创建网络：

```shell
version: "2"
services:
    mongodb:
        image: mongo:4
        container_name: devops-mongo # 容器名
        ports:
            - "27017:27017"
        volumes:
            - "/data/docker_local/mongo/configdb:/data/configdb"
            - "/data/docker_local/mongo/data/db:/data/db"
        command: --auth # 开启授权验证
        networks:
            - mongo_net
networks:
  mongo_net:
```

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g413yjfb2fj21e206o75z.jpg)

发现这时候没有报错了，但是它自动创建的网络好像也不叫 `mong_net` 而是根据规则，创建的 `db_mongo_net` 的网络。虽然不报错，但是我觉得有点别扭，查看了一下，可以利用 `name` 的标签定义一下网络。

```shell
version: "2.1"
services:
    mongodb:
        image: mongo:4
        container_name: devops-mongo # 容器名
        ports:
            - "27017:27017"
        volumes:
            - "/data/docker_local/mongo/configdb:/data/configdb"
            - "/data/docker_local/mongo/data/db:/data/db"
        command: --auth # 开启授权验证
        networks:
            - mongo_net
networks:
  mongo_net:
    name: mongo_net
```

这时候启动时，日志显示就比较符合预期了：

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g41480356uj21do06etag.jpg)

「好奇心宝宝」附身，如果我 name 指定想要的值，上一层，有必要一致吗？

```shell
version: "2.1"
services:
    mongodb:
        image: mongo:4
        container_name: devops-mongo # 容器名
        ports:
            - "27017:27017"
        volumes:
            - "/data/docker_local/mongo/configdb:/data/configdb"
            - "/data/docker_local/mongo/data/db:/data/db"
        command: --auth # 开启授权验证
        networks:
            - mongo_net
networks:
  default:
    name: mongo_net
```

上面的这种改法会报错：`ERROR: Service "mongodb" uses an undefined network "mongo_net"`。所以，顶层的 `networks` 下一层及的网络名称，要和服务中要加入的名称保持一致才行。这样一试，貌似对上面创建的 `db_mongo_net` 的网络而不报错的现象理解了。省略了 `name`, 那么 `name` 就按照默认规则创建网络了，其实就是类似于：

```shell
version: "2.1"
services:
    mongodb:
        image: mongo:4
        container_name: devops-mongo # 容器名
        ports:
            - "27017:27017"
        volumes:
            - "/data/docker_local/mongo/configdb:/data/configdb"
            - "/data/docker_local/mongo/data/db:/data/db"
        command: --auth # 开启授权验证
        networks:
            - mongo_net
networks:
  mongo_net:
    name: db_mongo_net
```

{% note warning no-icon %}
注意点：name 这个标签，这样的用法需要在 Compose 2.1 版本及以上才能使用
{% endnote %}

通过上面的对比，对 networks 有了初步了解了。那么，Compose 文件中式还不是一定要创建网络才行呢？可不可以利用已有的网络呢？后面的 external 会有介绍。

PS：通过 `docker network ls/rm/create ..` 等命令，可以查看或操作容器的网络。

### links

> Links allow you to define extra aliases by which a service is reachable from another service. They are not required to enable services to communicate - by default, any service can reach any other service at that service’s name. In the following example, db is reachable from web at the hostnames db and database:

通过链接，您可以给某个 service 定义别名，通过该别名可以从其他服务访问服务。默认情况下，任何服务都可以通过该服务的名称访问任何其他服务。下面的例子中，`db` 是一个服务名，在 `web` 服务中，给 `db` 定义了一个别名 `database`。那么，在 `web` 服务中，既可以通过 `db` 又可以通过 `databse` 查找到主机名了。

```shell
version: "3"
services:

  web:
    build: .
    links:
      - "db:database"
  db:
    image: postgres
```

### Specify custom networks

> Instead of just using the default app network, you can specify your own networks with the top-level networks key. This lets you create more complex topologies and specify custom network drivers and options. You can also use it to connect services to externally-created networks which aren’t managed by Compose.

为了不使用默认的网络，你可以使用 compsose 文件的 top-level 关键字 `networks` 自定义网络。这让你可以创建更复杂的拓扑并指定自定义网络驱动程序和选项。 你还可以使用它将服务连接到不由 Compose 管理的外部创建的网络。

> Here’s an example Compose file defining two custom networks. The proxy service is isolated from the db service, because they do not share a network in common - only app can talk to both.

这里有一个例子，自定义了两个网络。 `proxy` 服务和 `db` 服务是隔离的。因为它俩不共享一个网络，只有 `app` 服务能够和他俩都能通信。

```shell
version: "3"
services:

  proxy:
    build: ./proxy
    networks:
      - frontend
  app:
    build: ./app
    networks:
      - frontend
      - backend
  db:
    image: postgres
    networks:
      - backend

networks:
  frontend:
    # Use a custom driver
    driver: custom-driver-1
  backend:
    # Use a custom driver which takes special options
    driver: custom-driver-2
    driver_opts:
      foo: "1"
      bar: "2"
```

## external

`external: true` 加上这行表示我这个服务用的网络是用外部的网络，不用自动创建。否则，会按照规则默认创建网络的，例如 `db_default`、`sonarqube_default` 这些网络就是默认创建的。如果这个时候没有对应的外部网络，会弹出如下的提示：

```shell
Creating network "db_default" with the default driver
ERROR: Network mysql_net declared as external, but could not be found. Please create the network manually using `docker network create mysql_net` and try again.
```

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g415nm7o6jj21ki0ykn7l.jpg)

注意点：对于 compose 3.3 及更低版本，外部不能与其他网络配置键（driver，driver_opts，ipam，internal）一起使用。版本 3.4 及更高版本不再存在此限制。

external 标签[官网有详细介绍](https://docs.docker.com/compose/compose-file/#external-1)。

### Use a pre-existing network

如果你想要你的容器加入到一个已存在的网络中，使用 `external` 选项:

``` shell
version: "3.7"

services:
  proxy:
    build: ./proxy
    networks:
      - outside
      - default
  app:
    build: ./app
    networks:
      - default

networks:
  outside:
    external: true
```

这时候不会去创建一个 `[projectname]_outside` 的网络， Compose 会去查找一个已存在的叫做 `outside` 的网络，并且将它和 `proxy` 服务相连。

## 参考

- [官宣-dockerhub-sonarqube](https://hub.docker.com/_/sonarqube/)
- [compose-file-v2/#network](https://docs.docker.com/compose/compose-file/compose-file-v2/#networks)
- [compose/networking](https://docs.docker.com/compose/networking/) 专门讲解了 compose networking
