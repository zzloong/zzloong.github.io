---
title: Spring Boot 实战 —— 通过 Sentry 收集 Log4j2 异常日志记录
date: 2019-08-11 11:49:10
tags:
  - Java
  - Spring Boot
  - 日志
  - DevOps
categories:
  - Java
keywords:
  - log
  - log4j2
  - sentry
---

## 背景

在日常线上运行的应用中如果报了异常，怎么在第一时间收到报错告警并且查看报错日志呢？周五就因为缺乏报错日志，导致构建服务受影响超过了 2 小时，虽然不至于「死人」，但是也确实让我意识到日志的重要性！后来临时在相关的地方加上日志，通过错误信息很快定位了到了原因，原来是数据库被运维人员做了新特性的改动，而我们服务还未适配好。想想当时的场景，还真是有点紧张~囧

今天就学习一下利用 [Sentry](https://sentry.io/welcome/) 作为日志收集系统，结合之前学习的 [log4j2](https://michael728.github.io/2019/08/17/java-spring-boot-log4j2/) 日志输出，主动收集异常日志。

## Sentry

我们先来 Sentry 是怎么介绍自己的：

> Open-source error tracking that helps developers monitor and fix crashes in real time. Iterate continuously. Boost efficiency. Improve user experience

Sentry 是一款开源的错误跟踪系统，可帮助开发人员实时监控和修复崩溃。它是不断迭代的，提高效率，改善用户体验。

Sentry 本身的文档也记载的比较全面，强烈安利，一般问题可以通过阅读 [Doc](https://docs.sentry.io/) 学习或者[论坛咨询](https://forum.sentry.io/)。

OK，下面我们就开始通过 Docker 安装 Sentry。

PS：如果你的 Docker 环境还未配置好，可以阅读我之前的总结：[Linux——CentOS 安装 Docker 教程](https://michael728.github.io/2019/06/01/docker-centos-install/)

### 前提

在 [Sentry Installation 页面](https://docs.sentry.io/server/installation/)，官方做了总结，Sentry 需要和几个服务有交互：

- [PostgreSQL](http://www.postgresql.org/)：Docker image [postgres:9.5](https://hub.docker.com/_/postgres/)，这里明确版本是 9.5，我觉得非常棒！这保证了在未来部署的可重现，避免了不同版本差异造成部署失败的问题。
- [Redis](https://redis.io/)
  - 官方说如果你使用的 Ubuntu < 15.04，推荐安装 [chirs-lea/redis-server](https://launchpad.net/~chris-lea/+archive/ubuntu/redis-server)
  - 文档推荐的是 Docker image [redis:3.2-alpine](https://hub.docker.com/_/redis/).

硬件要求，具体的可以阅读[官方文档](https://docs.sentry.io/server/installation/#hardware)，我这里仅仅是家里测试使用，因此无所谓。

下面的步骤其实是官方文档的翻译，但是，跟着走了一遍之后发现，服务并不能正常运行起来，但是也没啥坏处，主要步骤其实就和这个差不多，可能是某些细节漏了，因此，你可以大体浏览一下步骤。如果不想浪费时间，可以直接跳到下面的 「脚本一键安装 Sentry」小节。

### 创建容器

需要 [Docker 版本 1.10+](https://www.docker.com/get-started)

克隆仓库 [getsentry/onpremise](https://github.com/getsentry/onpremise)。这个仓库是定制化构建你自己 Sentry 镜像的基础：

```shell
cd /data
git clone https://github.com/getsentry/onpremise.git
cd onpremise
```

仓库里的 `sentry.conf.py and config.yml` 可供你 [配置 Sentry](https://docs.sentry.io/server/config/) 所用，建议先浏览一下这个配置文档。

现在开始构建我们定制好的镜像。如果你构建的镜像需要推送到你本地的镜像仓中，那么可以用如下方式先定义好镜像名再构建：

```shell
REPOSITORY=registry.michael.com/sentry make build push
```

我本地没有镜像仓，只需要用如下方式构建即可：

```shell
make build
```

### 运行依赖的服务

Redis:

```shell
docker run \
  --detach \
  --name sentry-redis \
  redis:3.2-alpine
```

PostgreSQL:

```shell
docker run \
  --detach \
  --name sentry-postgres \
  --env POSTGRES_PASSWORD=secret \
  --env POSTGRES_USER=sentry \
  postgres:9.5
```

Outbound Email:

```shell
docker run \
  --detach \
  --name sentry-smtp \
  tianon/exim4
```

### 运行 Sentry 服务

`${REPOSITORY}` 只的是你刚刚定义的镜像名，如果没有，默认是 `sentry-onpremise`。

![REPOSITORY](https://gitee.com/michael_xiang/images/raw/master/cTlup8.png)

为了测试镜像是 OK 的，可以运行如下命令:

```shell
docker run \
  --rm ${REPOSITORY} \
  --help
```

现在可以生成一个 `secret-key` 值：

```shell
docker run \
  --rm ${REPOSITORY} \
  config generate-secret-key
```

生成的值可以在 `config.yml` 中设置给 `system.secret-key`,或者通过环境变量。如果是设置在 `config.yml` 文件中，这时候你必须重新构建你的镜像。

运行的基本命令举例如下：

```shell
docker run \
  --detach \
  --link sentry-redis:redis \
  --link sentry-postgres:postgres \
  --link sentry-smtp:smtp \
  --env SENTRY_SECRET_KEY='<secret-key>' \
  ${REPOSITORY} \
  <command>
```

{% note warn %}
官方文档里有一行小字，说明的是后面的文档，不会特地的写上 --link 去表示链接容器，但是这些都是必须的！同时，${REPOSITORY} 会被引用为 sentry-onpremise。
{% endnote %}

#### 运行 Web 服务

暴露的是 9000 的端口，这样部署之后，可以通过 `http://localhost:9000/` 访问。

```shell
docker run \
  --detach \
  --link sentry-redis:redis \
  --link sentry-postgres:postgres \
  --link sentry-smtp:smtp \
  --name sentry-web \
  --publish 9000:9000 \
  --env SENTRY_SECRET_KEY=${SENTRY_SECRET_KEY} \
  sentry-onpremise \
  run web
```

### 脚本一键安装 Sentry

发现了 [getsentry/onpremise/install.sh](https://github.com/getsentry/onpremise/blob/master/install.sh) 这个脚本，看懂这个脚本里的内容基本上就差不多知道怎么安装了。

读一下脚本：

- `LATEST_STABLE_SENTRY_IMAGE='sentry:9.1.2'` 安装的是 `sentry:9.1.2`
- 在 `docker-compose.yml` 中会去创建两个卷，分别是 `sentry-data`和 `sentry-postgres`，这和上面步骤是不是发现区别了，上面创建
`postgres` 时，可没有创建卷；
- `cp -n .env.example "$ENV_FILE"` 会将仓库下的 [.env.example](https://github.com/getsentry/onpremise/blob/master/.env.example) 文件重命名为 `.env` 文件，其实里面就是为了设置一个环境变量 `SENTRY_SECRET_KEY`
- `export SENTRY_IMAGE=$LATEST_STABLE_SENTRY_IMAGE` 命名了一个环境变量，记录了镜像名
- `docker-compose build` 开始构建镜像啦，其实就是类似这个命令 `docker build .`
- `SECRET_KEY=$(docker-compose run --rm web config generate-secret-key 2> /dev/null | tail -n1 | sed -e 's/[\/&]/\\&/g')` 运行命令获得 `secret-key` 值，赋值给了变量 `SECRET_KEY`
- `sed -i -e 's/^SENTRY_SECRET_KEY=.*$/SENTRY_SECRET_KEY='"$SECRET_KEY"'/' $ENV_FILE` 将 `SECRET_KEY` 变量值填写到环境变量文件 `.env` 中
- 接着就是设置数据库的步骤，这其实是就是为了在数据库中添加账号数据和其他一些数据表生成
- 最后就是执行 `cleanup` 函数

执行脚本之前，我们先把之前创建的几个服务删除掉吧：

```shell
docker stop sentry-smtp sentry-postgres sentry-redis
docker rm $(docker ps -aq)
```

下面开始安装步骤：

- `sh install.sh` 执行安装脚本，脚本执行完需要一点时间，运行完成之后，会退出。中间过程会让你选择是否创建账号：

![添加账号](https://gitee.com/michael_xiang/images/raw/master/XoQY9F.png)

- 接着运行 `docker-compose up -d` 即可；

![容器列表](https://gitee.com/michael_xiang/images/raw/master/zoG6sw.png)

PS：为何容器的名称是都是 `onpremise` 开头的呢？[因为不指定名称时，会默认取目录名的](https://docs.docker.com/compose/reference/overview/)。

这时候输入刚刚创建的账号登录：

![login](https://gitee.com/michael_xiang/images/raw/master/WWx2n1.png)

## 配置 Sentry

### 创建 project

那我之前的 log4j2 的 demo 作为演示，这里选择一个 Java 项目，并且，我还创建了一个叫 `spring-boot` 的 Team。

![project](https://gitee.com/michael_xiang/images/raw/master/VEi8nF.png)

## spring-boot 日志适配

[官方文档-Java](https://docs.sentry.io/clients/java/) 给出了适用于 Java 项目的全面的适配指南，咱们使用的是 [log4j2](https://docs.sentry.io/clients/java/integrations/#log4j-2x)。

### 引入依赖

```xml
<dependency>
    <groupId>io.sentry</groupId>
    <artifactId>sentry-log4j2</artifactId>
    <version>1.7.26</version>
</dependency>
```

### log4j2.xml 配置修改

- `configuration` 中加上 `packages="org.apache.logging.log4j.core,io.sentry.log4j2"`

下面示例中的 `SentryAppender` 表示发送 `warn` 级别的日志到 Sentry Server。`ConsoleAppender` 仅仅表示是一个示例，表示你项目中之前使用的非 sentryappener 的例子。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration status="warn" packages="org.apache.logging.log4j.core,io.sentry.log4j2">
    <appenders>
        <Console name="Console" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" />
        </Console>

        <Sentry name="Sentry" />
    </appenders>

    <loggers>
        <root level="INFO">
            <appender-ref ref="Console" />
            <!-- Note that the Sentry logging threshold is overridden to the WARN level -->
            <appender-ref ref="Sentry" level="WARN" />
        </root>
    </loggers>
</configuration>
```

经过测试，，因为原有项目中的 apppender 都是为了之前的作用设置的，比如控制台打印、比如输出到文件。要想将异常信息发送到 Sentry，这里的`SentryAppender` 是必不可少的。别忘了 `appender-ref` 也要设置！

### 配置 DSN（）

[配置页面](https://docs.sentry.io/clients/java/config/#configuration) 介绍了如何设置 DSN（Data Source Name）。

 进入 Sentry，项目的 DSN 在项目页面-》setings-》Clinet Keys(DSN) 中可以发现：

![DSN](https://gitee.com/michael_xiang/images/raw/master/dgVaSQ.png)

配置 DSN 有好几种方式，具体的可以在页面查看，这里介绍我采用的：

在 `resources` 文件夹下，新建 `sentry.properties`：

```xml
dsn=http://8d53042c89774e5dba599ee67c5c8804@192.168.3.43:9000/3
```

默认的就是 `sentry.properties`，一开始我直接写在了 `application.properties` 中，Sentry 怎么也收不到异常日志。

### 代码中

代码示例：

```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
import org.apache.logging.log4j.Marker;
import org.apache.logging.log4j.MarkerManager;

public class MyClass {
    private static final Logger logger = LogManager.getLogger(MyClass.class);
    private static final Marker MARKER = MarkerManager.getMarker("myMarker");

    void logSimpleMessage() {
        // This sends a simple event to Sentry
        logger.error("This is a test");
    }

    void logWithBreadcrumbs() {
        // Record a breadcrumb that will be sent with the next event(s),
        // by default the last 100 breadcrumbs are kept.
        Sentry.record(
            new BreadcrumbBuilder().setMessage("User made an action").build()
        );

        // This sends a simple event to Sentry
        logger.error("This is a test");
    }

    void logWithTag() {
        // This sends an event with a tag named 'log4j2-Marker' to Sentry
        logger.error(MARKER, "This is a test");
    }

    void logWithExtras() {
        // MDC extras
        ThreadContext.put("extra_key", "extra_value");
        // NDC extras are sent under 'log4j2-NDC'
        ThreadContext.push("Extra_details");
        // This sends an event with extra data to Sentry
        logger.error("This is a test");
    }

    void logException() {
        try {
            unsafeMethod();
        } catch (Exception e) {
            // This sends an exception event to Sentry
            logger.error("Exception caught", e);
        }
    }

    void unsafeMethod() {
        throw new UnsupportedOperationException("You shouldn't call this!");
    }
}
```

可以发现，Sentry 使用的接口和之前 log4j2 是有区别的：

```java
// 原来
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
private final Logger log = LoggerFactory.getLogger(this.getClass());
// sentry
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
private final Logger log = LogManager.getLogger(this.getClass());
```

实际过程中，这里是很多人忽视的，一定要仔细一点！

这时候，使用我们之前的错误接口故意打印错误日志，看看 Sentry 的捕获效果吧：

![error](https://gitee.com/michael_xiang/images/raw/master/HyqIlX.png)

## 示例代码

- [awesome-spring-boot-examples/log4j2](https://github.com/Michael728/awesome-spring-boot-examples/tree/master/spring-boot-log4j2)

## 参考
