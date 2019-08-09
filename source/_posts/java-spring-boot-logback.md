---
title: Spring Boot 实战 —— 日志框架 Log4j2 SLF4J 的学习
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
