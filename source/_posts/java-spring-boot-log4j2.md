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

Java 中比较常用的[日志框架](https://logging.apache.org/)：

- log4j(`Log for Java`)：Apache 的一个开源项目，七种日志级别：OFF、FATAL、ERROR、WARN、INFO、DEBUG、TRACE
- [logback](https://logback.qos.ch/)：是一个很成熟的日志框架，其实 logBack 和 log4j 出自一个人之手，这个人就是 Ceki Gülcü。logback 比 log4j 大约快 10 倍、消耗更少的内存，迁移成本也很低，自动压缩日志、支持多样化配置、不需要重启就可以恢复 I/O 异常等优势
- [log4j2](https://logging.apache.org/log4j/2.x/manual/index.html)：作者认为，log4j2已经不仅仅是 log4j 的一个升级版本了，而是从头到尾被重写的，这可以认为这其实就是完全不同的两个框架

<!-- more -->

Spring Boot 默认使用 logback，但相比较而言，log4j2 在性能上面会更好。SpringBoot 高版本都不再支持 log4j，而是支持 log4j2。log4j2，在使用方面与 log4j 基本上没什么区别，比较大的区别是 log4j2 不再支持 properties 配置文件，支持 xml、json 格式的文件。

《阿里巴巴Java开发手册》，其中有一条规范做了「强制」要求：

> 应用中不可直接使用日志系统（Log4j Logback）中的 API，而应依赖使用日志框架 SLF4J 中的 API，使用日志门面模式的日志框架，有利于维护和各个类的日志处理方式统一。

Java 简易日志门面（`Simple Logging Facade for Java`，缩写 [SLF4J](https://www.slf4j.org/)），它并不是真正的日志框架,他是对所有日志框架制定的一种规范、标准、接口，并不是一个框架的具体的实现，因为接口并不能独立使用，需要和具体的日志框架实现配合使用。可以在软件部署的时候决定要使用的 Logging 框架，目前主要支援的有 Java logging API、log4j 及 logback 等框架。

## 理解 SLF4J

接口用于定制规范，可以有多个实现，使用时是面向接口的(导入的包都是 slf4j 的包而不是具体某个日志框架中的包)，即直接和接口交互，不直接使用实现，所以可以任意的更换实现而不用更改代码中的日志相关代码。

比如：slf4j 定义了一套日志接口，项目中使用的日志框架是logback，开发中调用的所有接口都是 slf4j 的，不直接使用 logback，调用是 自己的工程调用 slf4j 的接口，slf4j 的接口去调用 logback 的实现，可以看到整个过程应用程序并没有直接使用 logback，当项目需要更换更加优秀的日志框架时(如log4j2)只需要引入 log4j2 的 jar 和 Llg4j2 对应的配置文件即可，完全不用更改 Java 代码中的日志相关的代码 `logger.info(“xxx”)`，也不用修改日志相关的类的导入的包( `import org.slf4j.Logger; import org.slf4j.LoggerFactory;`)

总结：使用日志接口便于更换为其他日志框架。

One More Thing：上面的这几段话是参考文章中截取的，也让我确实理解了为何推荐使用 SLF4J 的原因。这种做法感觉就是有点「面向接口编程」的思想，今天也查阅了一些这方面的资料，也让我想起了为何项目中写 Service 代码时，往往是先写个接口、然后在写个该接口的实现类。待有时间好好研究一些这块的优点！

## 性能分析

![性能评测](https://gitee.com/michael_xiang/images/raw/master/BBsUn0.jpg)

可以看到在同步日志模式下, Logback的性能是最糟糕的。

![异步日志模式](https://gitee.com/michael_xiang/images/raw/master/VqJmho.jpg)

log4j2的性能无论在同步日志模式还是异步日志模式下都是最佳的！那本文就介绍的是使用 log4j2 作为 slf4j 的具体实现。

## log4j2 依赖

```shell
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

## log4j2 使用

```shell
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

// 这了两种写法都 OK，推荐第一种，不用每次都要修改类名
private static final Logger logger = LoggerFactory.getLogger(this.getClass());
private static final Logger logger = LogManager.getLogger(UserController.class);
//...
logger.debug("this is debug");
logger.info("this is info");
```

## log4j2 日志级别

从小到大依次是:~~all~~、trace、debug、info、warn、error、~~fatal、off~~

由于我们使用的是 slf4j 接口包，该接口包中只提供了未标有删除线的日志级别的输出。

## log4j2 配置文件结构

配置文件的主要结构如下：

- Appenders:
  - Appender
    - Filter
    - Layout
    - Policies
    - Strategy
- Loggers
  - Logger
  - RootLogger

## Appender

Appender 可以理解为一个管道，定义了日志内容的去向(保存位置)。

- 配置一个或者多个 `Filter`。
- 配置 `Layout` 来控制日志信息的输出格式。
- 配置 `Policies` 以控制日志何时(When)进行滚动。
- 配置 `Strategy` 以控制日志如何(How)进行滚动。

注意点：

- 多个 `appender` 不能指向同一个日志文件，否则会报错：`Configuration has multiple incompatible Appenders pointing to the same resource 'logs/mybatis-demo-warn.log'`
- `ImmediateFlush=true`，一旦有新日志写入，立马将日志写入到磁盘的文件中。当日志很多，这种频繁操作文件显然性能很低下
- `immediateFlush`：log4j2 接收到日志事件时，是否立即将日志刷到磁盘。默认为 true。
- BufferedIO: 文件流写出是否使用缓冲，true 表示使用，默认值为 false 即不使用缓冲。测试显示，即使在启用immediateFlush 的情况下，设置 `bufferedIO=true` 也能提高性能。
- 一个 LogConfig 可以使用多个 appender，一个 appender 也可以被多个 LogConfig 使用

[官宣——Appender](http://logging.apache.org/log4j/2.x/manual/appenders.html#RandomAccessFileAppender)

### PatternLayout

这是常用的日志格式化类，其它日志格式化类很少用。

```shell
<PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" charset="UTF-8"/>
```

常用说明：

```shell
%d{HH:mm:ss.SSS} 表示输出到毫秒的时间
%t 输出当前线程名称
%-5level 输出日志级别，-5表示左对齐并且固定输出5个字符，如果不足在右边补0
%logger 输出logger名称，因为Root Logger没有名称，所以没有输出
%msg 日志文本
%n 换行

其他常用的占位符有：
%F 输出所在的类文件名，如Client.java
%L 输出行号
%M 输出所在方法名
%l 输出语句所在的行数, 包括类名、方法名、文件名、行数
```

关于 pattern 的格式点击 [官宣——Pattern Layout](http://logging.apache.org/log4j/2.x/manual/layouts.html#PatternLayout)

### Filter

Filters 决定日志事件能否被输出。过滤条件有三个值：`ACCEPT(接受)`，`DENY(拒绝)`，`NEUTRAL(中立)`。

常用的 Filter 实现类有：

- LevelRangeFilter
- TimeFilter
- ThresholdFilter

简单说就是 log4j2 中的过滤器 `ACCEPT` 和 `DENY` 之后，后续的过滤器就不会执行了，只有在 `NEUTRAL` 的时候才会执行后续的过滤器

```shell
<Console name="Console">

    <!--
        设置 onMismatch="NEUTRAL" 可以让日志经过后续的过滤器
        最后一个过滤器建议设置 onMismatch="DENY", 不然日志就输出了。
    -->
    <Filters>

        <!-- 从大到小：error, warn, info, debug, trace -->
        <LevelRangeFilter minLevel="error" maxLevel="info" onMatch="ACCEPT" onMismatch="NEUTRAL" />

        <!-- 只允许在每天的 8点~8点半 之间输出日志 -->
        <TimeFilter start="08:00:00" end="08:30:00" onMatch="ACCEPT" onMismatch="DENY" />
    </Filters>

    <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" charset="UTF-8"/>
</Console>
```

LevelRangeFilter 对它们进行了 `ACCEPT`，而剩下的 `trace Msg` 和 `debug Msg` 则会经过下一个过滤器。

### Policy

`Policy & Strategy`

- Policy 是用来控制日志文件何时(When)进行 Rolling/滚动的；
- Strategy 是用来控制日志文件如何(How)进行 Rolling/滚动的。

所谓「日志滚动」就是当达到设定的条件后，日志文件进行切分。比如：工程师想让系统中的日志按日进行切分，并且按月归档。

`Rolling` 的意思是当满足一定条件后，就重命名原日志文件用于备份，并重新生成一个新的日志文件。例如需求是每天生成一个日志文件，但是如果一天内的日志文件体积已经超过 1G，就重新生成。**两个条件满足一个即可**。

Policy常用的实现类:

- `SizeBasedTriggeringPolicy`，根据日志文件的大小进行滚动。单位有：`KB`，`MB`，`GB`
- `CronTriggeringPolicy`，使用 Cron 表达式进行日志滚动，很灵活
- `TimeBasedTriggeringPolicy`，这个配置需要和 `filePattern` 结合使用，注意 `filePattern` 中配置的文件重命名规则。滚动策略依赖于 `filePattern` 中配置的最具体的时间单位，根据最具体的时间单位进行滚动。这种方式比较简洁。`CronTriggeringPolicy` 策略更强大

在 `TimeBasedTriggeringPolicy` 标签中加上了 `modulate` 属性并设置为 `true`，该属性的意思是是否对日志生成时间进行调制。若为 `true`，则日志时间将以 0 点为边界进行偏移计算。例如第一次日志保存时间是 3 点，`modulate`为 `true`，`interval` 是 `4h`。那么下次生成日志时间是 4点，08:00，12:00……。

```shell
<Appenders>
    <RollingRandomAccessFile name="File" fileName="logs/app.log"
                                filePattern="logs/$${date:hh-mm}/%d{hh-mm-ss}.app.%i.log" >
        <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" charset="UTF-8"/>

        <Policies>
            <!-- 每 5s 翻滚一次 -->
            <!--<CronTriggeringPolicy schedule="0/5 * * * * ?" />-->

            <!--
                filePattern中最具体的时间单位是 秒。
                这里用 TimeBasedTriggeringPolicy 替换 CronTriggeringPolicy

                注意：modulate属性是指从启动时间开始算5秒，还是从0秒开始算5秒，运行一下就明白了。
                modulate: true(默认值) // 会从启动时间开始算 5秒
                modulate: false // 从 0秒开始算
            -->
            <TimeBasedTriggeringPolicy interval="5" modulate="true"/>
            <SizeBasedTriggeringPolicy size="10 MB"/>
        </Policies>
        <DefaultRolloverStrategy max="10" />
    </RollingRandomAccessFile>
</Appenders>
```

### Strategy

Strategy常用的实现类：

- DefaultRolloverStrategy
- DirectWriteRolloverStrategy

这两个 Strategy 都是控制如何进行日志滚动的。

`DefaultRolloverStrategy` 默认的 `max`为 7。

```shell
<DefaultRolloverStrategy max="7"/>
```

max 参数指定了计数器的最大值。一旦计数器达到了最大值，过旧的文件将被删除。

注意：不要认为 max 参数是需要保留的日志文件的最大数目。

max 参数是与 `filePattern` 中的计数器 `%i` 配合起作用的，其具体作用方式与 `filePattern` 的配置密切相关。

1.如果filePattern中仅含有date/time pattern，每次rollover时，将用当前的日期和时间替换文件中的日期格式对文件进行重命名。max参数将不起作用。

如，`filePattern="logs/app-%d{yyyy-MM-dd}.log"`

2.如果 `filePattern` 中仅含有整数计数器（即`%i` ），每次 `rollover` 时，文件重命名时的计数器将每次加1（初始值为1），若达到 max 的值，将删除旧的文件。

如，`filePattern="logs/app-%i.log"`

3.如果 `filePattern` 中既含有 `date/time pattern`，又含有 `%i`，每次 `rollover` 时，计数器将每次加 1，若达到 max 的值，将删除旧的文件，直到 `data/time pattern` 不再符合，被替换为当前的日期和时间，计数器再从1开始。

如，`filePattern="logs/app-%d{yyyy-MM-dd HH-mm}-%i.log"`

### Appender 类型

#### FileAppender(File)、RandomAccessFileAppender(RandomAccessFile)

- 相同点：写入日志信息到文件
- 不同点：使用的 `I/O` 实现类不同，前者使用 `FileOutputStream`，后者使用 `RandomAccessFile`。

官方文档说是在 `bufferedIO=true` (默认是 `true` )的情况下，性能提升 `20% ~ 200%`。

常用属性:

- `fileName`：来指定文件位置，文件或目录不存在则会自动创建。
- `immediateFlush`：是否每次写入都要立刻刷新到硬盘中。默认 `true`，如果使用默认值可能会影响性能。

#### RollingFileAppender(RollingFile)、RollingRandomAccessFileAppender(RollingRandomAccessFile)

- 相同点：写入日志信息到文件
- 不同点：使用的 `I/O` 实现类不同，前者使用 `FileOutputStream`，后者使用 `RandomAccessFile`。
- 上一对的实现类不能进行「日志滚动」，而带有 `rolling` 字样的 appender 就可以实现「滚动」功能。有「滚动」，会判断是否满足封存文件的要求，执行日志存档操作。

> RollingRandomAccessFile Appender，相比 RollingFileAppender有很大的性能提升，官网宣称是20-200%

```shell
<RollingRandomAccessFile name="File" fileName="logs/app.log"
                            filePattern="logs/$${date:hh-mm}/%d{hh-mm-ss}.app.%i.log" >
    <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" charset="UTF-8"/>
    <Policies>
        <!-- 每 5s 翻滚一次 -->
        <CronTriggeringPolicy schedule="0/5 * * * * ?" />
        <SizeBasedTriggeringPolicy size="10 MB"/>
    </Policies>
    <DefaultRolloverStrategy max="10" />
</RollingRandomAccessFile>
</Appenders>
```

常用属性：

- `filePattern`：指定当发生Rolling时，文件的转移和重命名规则。至于其中的 `$${date:hh-mm}` 是表示了一个文件夹（以 `小时-分钟`）命名。
- `DefaultRolloverStrategy` 指定了如何(How)进行翻滚，并且指定了最大翻滚次数(影响%i参数值)，超过次数之后会按照相应的规则删除旧日志。
- `Policies`: 这里就是规定了何时进行滚动(When)，可以有多个Policy。
  - `CronTriggeringPolicy`，比如设置每 5s 进行一次翻滚
  - `SizeBasedTriggeringPolicy` 指定当文件体积大于size指定的值时，触发Rolling。例如，如果当前文件超过了 10MB，但是文件的名字还没有进行翻滚(建立新文件)，那么就会用%i的方式进行翻滚。

如果配置的是 `RollingFile` 或 `RollingRandomAccessFile`，则必须配置一个 `Policy`。

#### 翻滚理解

假设计数器次数设为2次 `<DefaultRolloverStrategy max="2" />`，`filePattern` 中既含有 `date/time pattern`，又含有 `%i`。

当满足翻滚触发条件时（时间间隔到了 OR 文件大小超了），就会启动 `Rolling`：

app.log

第一次翻滚：`app.log app.1.log // app.log -> app.1.log`
第二次翻滚：`app.log app.1.log app.2.lop // app.log -> app.2.log`

一个循环结束，到达了最大保存数 2 了，那么，`app1.log` 会被删除，下一个 `app3.log` 就会覆盖 `app2.log`，`app2.log`会改名为`app1.log`

第三次翻滚：`app.log app.2.lop app.3.lop // app.log -> app.3.log`
第四次翻滚：`app.log app.3.lop app.4.lop // app.log -> app.4.log`

理解：编号最近的一次也就是最新的一次 log，而采取了 `Policy` 方式的日志，`fileName` 中保存的日志将不会是全量的日志，而是根据你 `Policy` 的条件切分后的最近一次的日志内容。

- [博客园-Log4j2中RollingFile的文件滚动更新机制](https://www.cnblogs.com/yeyang/p/7944899.html) 滚动机制介绍的很详细
- [CSDN-log4j2教程【RollingFileAppender】](https://blog.csdn.net/u013066244/article/details/72461105)

#### 一个 Appender 示例

按月归档日志，按日进行切分，限制单文件大小为 500MB, 一天最多生成20个文件，也就是(20 * 500)MB大小的日志

```shell
<?xml version="1.0" encoding="UTF-8"?>
<Configuration name="baseConf" status="warn" monitorInterval="30">

    <Appenders>
        <RollingRandomAccessFile name="File" fileName="logs/app.log"
                                 filePattern="logs/$${date:yyyy-MM}/%d{yyyy-MM-dd}.app.%i.log" >
            <PatternLayout pattern="%d{HH:mm:ss.SSS} [%t] %-5level %logger{36} - %msg%n" charset="UTF-8"/>
            <Policies>
                <TimeBasedTriggeringPolicy interval="1" modulate="false"/>
                <SizeBasedTriggeringPolicy size="500MB"/>
            </Policies>
            <DefaultRolloverStrategy max="20" />
        </RollingRandomAccessFile>
    </Appenders>

    <Loggers>
        <Root level="info">
            <AppenderRef ref="File"/>
        </Root>
    </Loggers>

</Configuration>
```

## Logger

简单说 Logger 就是一个路由器，指定类、包中的日志信息流向哪个管道，以及控制他们的流量(日志级别)

Logger 部分为两个 Logger:

- RootLogger(必须配置)
- Logger

注意：Logger 中也可以加过滤器的！

### 日志重复打印问题

如果 Root 中的日志包含了 Logger 中的日志信息，并且 AppenderRef 是一样的配置，则日志会打印两次。

这时候我们需要使用一个 Logger 的属性来解决，那就是 `additivity`，其默认值为 `true`，需要配置为`false`

```shell
<?xml version="1.0" encoding="UTF-8"?>
<Configuration name="baseConf" status="warn" monitorInterval="30">

    <Appenders>
        <Console name="Console">
            <PatternLayout>
                <Pattern>%d %p %c{1.} [%t] %m%n</Pattern>
            </PatternLayout>
        </Console>
    </Appenders>

    <Loggers>
        <Logger name="me.master.snail.log.LogMain" level="info" additivity="false">
            <AppenderRef ref="Console"/>
        </Logger>

        <Root level="trace">
            <AppenderRef ref="Console"/>
            <Filters>
                <LevelRangeFilter minLevel="error" maxLevel="info" onMatch="ACCEPT" onMismatch="DENY" />
            </Filters>
        </Root>
    </Loggers>
</Configuration>
```

- RootLogger 只能有 1 个，普通的 Logger 可以定义多个，可以细致到给某个类定义；
- 多个 Logger 配置重复了，在日志文件中会重复；
- 每一个 Logger 对应的 name 是包路径，表示在 name 包下的类使用 AppenderRef 指向的日志模板来输出日志；
- 不同的 LogConfig 之间其实是有继承关系的，子 LogConfig 会继承 parent 的属性，而所有 LogConfig 都继承自 Root LogConfig。所以即使只配置了 root logger，你一样可以在任何地方通过  `LoggerFactory.getLogger` 获取一个 logger 对象，记录日志;
- 先配置一个 Root，让所有需要使用日志的 logger 继承，然后对有特别需要的 logger 进行特殊的配置，比如我们希望 `org.springframework` 包只记录 `error`以及 `warn` 级别的 log，再比如，我们希望能显示mybatis 执行的 sql 的日志，都可以进行个性化的配置；

### Logger 等级实验

```shell
<logger name="org.springframework" level="INFO" additivity="true">
    <AppenderRef ref="InfoLog"/>
</logger>

<Root level="ERROR" additivity="true">
    <AppenderRef ref="Console"/>
    <AppenderRef ref="InfoLog"/>
    <AppenderRef ref="WarnLog"/>
    <AppenderRef ref="ErrorLog"/>
</Root>
```

- ROOT 等级设为 `ERROR` 时，`org.springframework` Logger 等级设为 `OFF` 时，发现原来的 `warn.log` 和 `info.log` 文件中，都只有级别大于或等于 ERROR 的日志信息了；
- ROOT 等级设为 `ERROR` 时，`org.springframework` Logger 等级设为 `INFO` 时，发现`info.log` 文件中，增加了 `org.springframework` 包的相关 `INFO` 级别的日志信息了；

总结：

- Logger 日志等级和 appender 日志等级的关系：logger 日志等级和 appender 日志等级，谁「高」听谁的；
- 普通 Logger 的优先级高

## 效果

![log4j2-console](https://gitee.com/michael_xiang/images/raw/master/8PAZgl.png)

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
