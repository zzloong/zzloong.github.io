---
title: Spring Boot 实战 —— 日志框架 logback 的学习
date: 2019-08-17 23:49:10
tags:
  - Java
  - Spring Boot
  - 日志
categories:
  - Java
keywords:
  - log
---

## 背景

![logging.apache](https://gitee.com/michael_xiang/images/raw/master/joePal.png)

上一篇文章 [Spring Boot 实战 —— 日志框架 Log4j2 SLF4J 的学习](https://michael728.github.io/2019/08/17/java-spring-boot-logs/) 中已经介绍了常用的日志框架的比较，本文介绍另外一个主流的日志框架 [logback](https://logback.qos.ch/)

<!-- more -->

## 展示

![logback-console](https://gitee.com/michael_xiang/images/raw/master/gqmuIb.png)

## 看懂日志

- 时间戳，精确到毫秒
- logback 日志级别：日志级别分为：TRACE、DEBUG、INFO、WARN、ERROR、FATAL
- 进程 ID：进程 ID 指的是当前应用对应的 PID
- 分隔符：用于区分实际日志消息的开始
- 线程名称：括在方括号中
- 记录器名称：一般使用类名
- 日志内容：日志输出的打印内容

## 控制台输出

默认情况下， Spring Boot 只会记录 INFO、WARN、ERROR 级别的日志打印在控制台。Spring Boot 可以使用「调试模式」，会打印一些比较详细的额外信息，可以选用如下两种方式：

- 启动 JAR 模式时，加上 `--debug`,例如：`java  -jar <app.jar> --debug`；
- 在配置文件中配置属性 `debug=true`；

{% note info no-icon %}
这里的 DEBUG 模式只是会多打印系统默认的一些调用信息，并不会多打印你指定的 DEBUG 级别日志信息，除非你指定日志级别。
{% endnote %}

## 日志级别设置

下面两种格式常用：

- `logging.level.root=WARN` root 日志以 WARN 级别输出信息；
- `logging.level.com.michael.springbootlogback.congtroller=DEBUG` 指定包下的类以 DEBUG 级别输出；

`root` 表示 root 节点，它是必选节点，用来指定最基础的日志输出级别，默认是 INFO 级别，所以，此时若是不修改默认值，你直接指定其他包下的日志界别为 DEBUG 级别，其实也不会生效的。

另外，也可以设置日志组来批量设置日志级别，比如设定 `com.michael.controller` 和 `com.michael.service` 为同一组（包和包之间用英文逗号分隔）：

```java
logging.group.michael=com.michael.controller,com.michael.service
```

设置完日志组之后，给 michael 组设定日志级别：

```java
logging.level.michael=INFO
```

Spring Boot 默认提供两个日志组：

- `logging.group.web=org.springframework.core.codec,org.springframework.http,org.springframework.web`
- `logging.group.sql=org.springframework.jdbc.core`

## 日志文件输出

只需要在 Profile 配置文件，例如 `application.properties` 中设置 `logging.file` 或者 `logging.path` 属性即可：

- `logging.file` 设置日志文件，可以设置文件的绝对路径也可以设置相对路径，相对路径的话是相对应用的根目录，比如 `logging.file=logs/demo.log`;
- `logging.path` 设置日志的路径，也是既支持绝对路径也支持相对路径，比如 `logging.path=logs`，日志文件默认会存为 `spring.log`；

如果上面两个属性同时配置了，只有 `logging.file` 会生效；在日志文件达到 10MB 时，Spring Boot 会自动分割日志，当然，这个大小是可以通过 `logging.file.max-size` 属性更改的，下面谈谈有哪些配置属性。

## 日志配置

除了上面介绍的配置属性外，还有其他一些属性，例如：

- `logging.config` 日志配置；
- `logging.file.max-size` 最大日志文件大小；
- `logging.file.max-history` 最大归档文件数量；
- `logging.pattern.console` 控制台输出的日志模式；
- `logging.pattern.dateformat` 日志的日期格式；
- `logging.pattern.file` 默认使用日志模式
- `logging.pattern.level` 日志级别

### XML 配置日志

Spring Boot 中的 logback 默认使用 `src/main/resources` 文件夹下的 `logback.xml` 或 `logback-spring.xml` 作为日志配置。Spring Boot 官方推荐优先使用带有 `-spring` 的文件名作为日志配置文件。因为如果命名为 `logback-spring.xml` 日志配置，就可以在日志输出的时候引入一些 Spring Boot 特有的配置项。也可以通过自定义的方式指定配置文件： `logging.config=classpath:logback-confg.xml`

`application.properities` 配置：

```java
# 日志配置信息
## 这个路径其实不指定也可以，因为 logback-spring.xml 是默认配置文件名之一
logging.config=classpath:logback-spring.xml
## 这里的配置是为了在 xml 配置中应用的
log.level=info
log.path=logs
log.name=michael-demo
```

一个简单的控制台输出配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- application.properities中配置的变量 -->
    <springProperty scope="context" name="logLevel" source="log.level"/>
    <springProperty scope="context" name="logPath" source="log.path"/>
    <springProperty scope="context" name="logName" source="log.name"/>

    <appender name="Console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [%thread] %logger{80} - %msg%n</pattern>
            <charset>UTF-8</charset>
        </encoder>
    </appender>
    <root level="${logLevel}}">
        <appender-ref ref="Console"/>
    </root>
</configuration>
```

说明：

- `appender` 的 `name` 其实只是取的名字，你也可以叫它 `STDOUT`
- `%d` 表示时间，用 `%d{yyyy-MM-dd HH:mm:ss.SSS}` 定义了格式化时间，也可以使用 `%date`
- `%-5level` 表示显示日志级别，并且用 5 个字符靠左对齐，也可以用 `%p` 表示日志级别
- `%thread` 表示显示日志进程名字
- `%logger{80}` 表示日志输出者的名字，常常是类名
- `%msg` 日志消息
- `%n` 平台的换行符
- `%c` 用来在日志上输出类的全名
- `charset` 设置日志编码格式为 UTF-8，避免中文乱码
- `root` 标签内设置日志级别 `level`，等同于在配置文件中设置 `logging.pattern.level`

Logback 配置文件可以使用 property 标签自定义属性，然后在配置文件中使用，上面的配置可以这么写：

```java
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- application.properities中配置的变量 -->
    <springProperty scope="context" name="logLevel" source="log.level"/>
    <springProperty scope="context" name="logPath" source="log.path"/>
    <springProperty scope="context" name="logName" source="log.name"/>
    <property name="CONSOLE_LOG" value="$CONSOLE_LOG:%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [%thread] %logger{36} - %c - %msg%n"/>

    <appender name="Console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>${CONSOLE_LOG}}</pattern>
            <charset>UTF-8</charset>
        </encoder>
    </appender>
    <root level="${logLevel}}">
        <appender-ref ref="Console"/>
    </root>
</configuration>
```

## 示例代码

- [awesome-spring-boot-examples](https://github.com/Michael728/awesome-spring-boot-examples/blob/master/spring-boot-logs/src/main/resources/log4j2.xml)

## 参考

Log4j2

- [Github-apache/logging-log4j2](https://github.com/apache/logging-log4j2)
- [掘金-zdran-Spring Boot 学习笔记(二) 整合 log4j2](https://juejin.im/entry/5b35f1e86fb9a00e315c330e) 博主写了一些 Spring Boot 教程
- [博客园-蜗牛大师-浅谈Log4j2日志框架及使用](https://www.cnblogs.com/wuqinglong/p/9516529.html) 介绍的非常详细，强烈推荐！
- [博客园-Log4j2之Appenders](http://www.cnblogs.com/elaron/archive/2013/02/17/2914633.html) 对 appender 介绍详细
- [SpringBoot + Log4j2使用配置](https://www.jianshu.com/p/46b530446d20) 异步日志介绍的比较多
- [博客园-Springboot整合log4j2日志全解](https://www.cnblogs.com/keeya/p/10101547.html) 博客主题略酷，引用了其性能评测的图，似乎原文是这个 [logback log4j log4j2 性能实测](https://blog.souche.com/logback-log4j-log4j2shi-ce/)

SLF4J

- [掘金—HollisChuang—为什么阿里巴巴禁止工程师直接使用日志系统(Log4j、Logback)中的 API](https://juejin.im/post/5c11c831e51d4511624d1b59)
