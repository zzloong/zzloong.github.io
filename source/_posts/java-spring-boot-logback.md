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

![logback-console](https://gitee.com/michael_xiang/images/raw/master/B27jyG.jpg)

## 看懂日志

- 时间戳，精确到毫秒
- logback 日志级别：日志级别分为：TRACE、DEBUG、INFO、WARN、ERROR、FATAL
- 进程 ID：进程 ID 指的是当前应用对应的 PID
- 分隔符：用于区分实际日志消息的开始
- 线程名称：括在方括号中
- 记录器名称：一般使用类名
- 日志内容：日志输出的打印内容

## 日志依赖

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-logging</artifactId>
</dependency>
```

实际开发中我们不需要直接添加该依赖 `spring-boot-starter-logging`，因为 `spring-boot-starter` 其中包含了 `spring-boot-starter-logging`，该依赖内容就是 Spring Boot 默认的日志框架 logback。

PS：[上一篇文章](https://michael728.github.io/2019/08/17/java-spring-boot-logs/) 中说明了，如果要采用 log4j2，那么需要排除 Spring Boot 自带的日志：

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <!-- 去掉logback配置 -->
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>

    <!-- 引入log4j2依赖 -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-log4j2</artifactId>
    </dependency>
</dependencies>
```

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
- `%L` 表示行号
- `charset` 设置日志编码格式为 UTF-8，避免中文乱码
- `root` 标签内设置日志级别 `level`，等同于在配置文件中设置 `logging.pattern.level`

Logback 配置文件可以使用 property 标签自定义属性，然后在配置文件中使用。下面附上一个较复杂的配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!-- application.properities中配置的变量 -->
    <springProperty scope="context" name="logProfile" source="log.profile"/>
    <springProperty scope="context" name="logFileLevel" source="log.file.level"/>
    <!-- 定义日志 pattern -->
    <property name="logPattern"
              value="%d{yyyy-MM-dd HH:mm:ss.SSS} %-5level [%thread] %logger{36} - %c - %L{4} - %msg%n"/>
    <!-- 定义日志文件名称 -->
    <property name="appName" value="logback-demo"></property>
    <property name="logPath" value="log"></property>


    <!-- ch.qos.logback.core.ConsoleAppender 表示控制台输出 -->
    <appender name="console" class="ch.qos.logback.core.ConsoleAppender">
        <!--
        日志输出格式：
            %d表示日期时间，
            %thread表示线程名，
            %-5level：级别从左显示5个字符宽度
            %logger{50} 表示logger名字最长50个字符，否则按照句点分割。
            %L 表示行号
            %msg：日志消息，
            %n是换行符
        -->
        <encoder>
            <!--<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>-->
            <pattern>${logPattern}</pattern>
            <charset>UTF-8</charset>
        </encoder>
    </appender>

    <!-- 滚动记录文件，先将日志记录到指定文件，当符合某个条件时，将日志记录到其他文件 -->
    <appender name="appLogFileAppender" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <encoder>
            <pattern>${logPattern}</pattern>
            <charset>UTF-8</charset>
        </encoder>
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--
            滚动时产生的文件的存放位置及文件名称 %d{yyyy-MM-dd}：按天进行日志滚动
            %i：当文件大小超过maxFileSize时，按照i进行文件滚动
            -->
            <fileNamePattern>${logPath}/${appName}/%d{yyyy-MM-dd}-%i.log</fileNamePattern>
            <!--
            可选节点，控制保留的归档文件的最大数量，超出数量就删除旧文件。假设设置每天滚动，
            且maxHistory是365，则只保存最近365天的文件，删除之前的旧文件。注意，删除旧文件是，
            那些为了归档而创建的目录也会被删除。
            -->
            <MaxHistory>365</MaxHistory>
            <!--
            当日志文件超过maxFileSize指定的大小是，根据上面提到的%i进行日志文件滚动 注意此处配置SizeBasedTriggeringPolicy是无法实现按文件大小进行滚动的，必须配置timeBasedFileNamingAndTriggeringPolicy
            -->
            <timeBasedFileNamingAndTriggeringPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedFNATP">
                <maxFileSize>50MB</maxFileSize>
            </timeBasedFileNamingAndTriggeringPolicy>
        </rollingPolicy>
        <!--下面通过两个 Filter，记录了 WARN 和 Error 级别的日志，其实用 logger 来定义level，更方便-->
        <!--<filter class="ch.qos.logback.classic.filter.LevelFilter">-->
        <!--<level>warn</level>-->
        <!--<onMatch>ACCEPT</onMatch>-->
        <!--<onMismatch>NEUTRAL</onMismatch>-->
        <!--</filter>-->
        <!--<filter class="ch.qos.logback.classic.filter.LevelFilter">-->
        <!--<level>error</level>-->
        <!--<onMatch>ACCEPT</onMatch>-->
        <!--<onMismatch>DENY</onMismatch>-->
        <!--</filter>-->
    </appender>


    <!--<root level="error">-->
    <!--<appender-ref ref="console"/>-->
    <!--</root>-->
    <!--<logger name="com.michael.springbootlogback" >-->
    <!--<appender-ref ref="appLogFileAppender"/>-->
    <!--</logger>-->
    <springProfile name="${logProfile}">
        <!--<logger name="org.springframework" level="info" additivity="false"></logger>-->
        <logger name="com.michael" level="info">
            <appender-ref ref="console"/>
        </logger>
        <logger name="com.michael" level="${logFileLevel}">
            <appender-ref ref="appLogFileAppender"/>
        </logger>
    </springProfile>
</configuration>
```

输出到 console:

- `ConsoleAppender` 需要设置这样的一个 apppender，否则日志不会打印到控制台；

输出到文件：

- `RollingFileAppender` 可以实现日志的切分
- `rollingPolicy` 设置滚动切分的规则
- `totalSizeCap` 用来指定日志文件的上限大小，这个设置在 `maxFileSize` 之后起效，也就是说，如果你文件上限设置的是 1MB，但是 `maxFileSize` 设置的是 10MB，那么，这个日志文件也会保存为 10MB。假设你 `maxFileSize` 设置的是 1MB，`totalSizeCap` 设置的是 2MB，那么你日志文件的个数最多也就 2 个；
- `MaxHistory` 可选节点，控制保留的归档文件的最大数量，超出数量就删除旧文件。假设设置每天滚动，且maxHistory是365，则只保存最近365天的文件。我还是喜欢这个设置，比 `totalSizeCap` 意义更清晰；

`<root>` 可以包含零个或多个 `<appender-ref>` 元素，标识这个 appender 将会添加到这个 loger，logger 的属性：

- name: 用来指定受此 loger 约束的某一个包或者具体的某一个类，没写的时候会报错
- level：日志打印级别，如果未设置此属性，那么当前 logger 会继承上级的级别，也就是 root 的级别；
- addtivity：是否向上级 loger 传递打印信息。默认是 true

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
