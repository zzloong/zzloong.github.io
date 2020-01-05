---
title: Spring Boot 实战 —— 入门
date: 2019-07-07 17:49:10
tags:
  - Spring Boot
  - Java
categories:
  - Java
keywords:
  - Spring Boot
---

![springboot2](https://i.bmp.ovh/imgs/2019/07/713950737e3e2019.jpeg)

## 简介

目前没有系统学习过 Spring 框架，参与工作时，直接参与到了 Spring Boot 项目的开发。目前还比较菜，所以，你要是和我一样，不妨也跳过 Spring 框架的学习，直接学习 Sring Boot。

<!-- more -->

[官方文档](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#getting-started-introducing-spring-boot)的一段介绍：

> Spring Boot makes it easy to create stand-alone, production-grade Spring-based Applications that you can run. We take an opinionated view of the Spring platform and third-party libraries, so that you can get started with minimum fuss. Most Spring Boot applications need very little Spring configuration.

> You can use Spring Boot to create Java applications that can be started by using java -jar or more traditional war deployments. We also provide a command line tool that runs “spring scripts”.

大体意思是说，Spring Boot 可以轻松创建可以运行的独立的，基于生产级 Spring 的应用程序。这个框架简化了我们 Spring 的配置。可以使用 Spring Boot 创建 Java 应用，只需要使用  `java -jar` 或 `war` 包部署方式就可以启动。也内嵌了 `Tomcat`，在开发时无需以 `war` 包也可以运行应用。

## 示例代码

- [awesome-spring-boot-examples](https://github.com/Michael728/awesome-spring-boot-examples)

## 环境

下面是本文编写时，我机器的环境：

- Java 1.8.0_181
- Maven 3.5.4

## 创建项目

本文目的是创建一个基本的 RESTful Web 服务。创建 Spring Boot 服务的方式主要有两种：

- 在 IDEA 中使用 `Spring Initializr` 创建，我个人比较倾向这种方式，方便快捷；
- 访问网站 [Spring Initializr](http://start.spring.io/) 网站，勾选相关项目依赖，最后生成一个初始化项目，导入 IDE。

![Spring Initializr 网站](https://i.bmp.ovh/imgs/2019/07/492cf11fedb2b02e.jpg)

下面主要介绍 IDEA 如何初始化一个 Sring Boot 项目：

1.File-New-Porject，选择 `Spring Initializr`，选择 SDK 版本；
2.输入项目的元数据信息，关系到项目的路径、`pom` 文件中项目的 Maven 坐标（`GAV`）；

![gav](https://i.bmp.ovh/imgs/2019/07/b563013b9d013d48.png)

3.选择需要的 Maven 依赖，这里 Spring Boot 版本，我选择了 `2.1.6` 版本；

![dependency](https://i.bmp.ovh/imgs/2019/07/cb3afc79300ddea4.png)

4.最后一步指定项目存放位置；

- `src/main/java` 中有项目代码文件，根目录下是入口类：`SpringBootHelloWorldApplication` 类。`@SpringBootApplication` 注解，这是整个 Spring Boot 的核心注解，它的目的就是开启 Spring Boot 的自动配置。
- `src/main/resources` 下是配置文件：`application.properties`
- `src/test/` 下的测试入口：Chapter1ApplicationTests

## 添加控制器类 —— Controller

通常在项目中对外提供的 API 都会放在叫做 `Controller` 的包下。

我们创建一个 `Controller` 的包，并添加一个叫做 `HelloWorld` 的类：

```java
@RestController
public class HelloWorld {


    @GetMapping("/hello")
    public String sayHello() {
        return "Hello World";
    }

}
```

- `@RestController` 注解加在这个类上，使之变为一个 `Controller`

这是我们启动项目，便可以通过地址 `localhost:8080/hello` 或 `127.0.0.1:8080/hello` 看到 `sayHello` 函数执行的内容。

![启动](https://ws1.sinaimg.cn/large/6d9475f6ly1g4rgt2r9iuj20wr0fbmzx.jpg)

查看控制台的输出，我们可以知道 Spring Boot 项目启动时，默认的端口是 `8080`

![default port](https://i.bmp.ovh/imgs/2019/07/5537604915e92eee.png)

## Profile 配置文件

官方文档中有关于 [Profile](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-external-config-profile-specific-properties) 的描述。 `Prorile` 有 `轮廓、外形、简况`的含义，这里我就把它理解为「配置描述」好了。`src/main/resources` 目录是 Spring Boot 的配置目录，Spring Boot 的默认配置文件位置为：`src/main/resources/application.properties`。

在实际项目中，生产、beta 不同环境将采用不同的配置，比如数据库配置等等。这时候，我们只需要创建多分 Profile 文件即可。

除了 `application.properties` 文件，配置文件还可以采用下面的命名规则`application-{profile}.properties`。Environment 中具有一组可选的值。如果没有设置需要激活什么配置文件，就默认激活 `default` 配置，即 `application-default.properties`。

{% note info%}
指定的配置文件都是从同一个位置被激活，即从标准的配置文件 `application.properties`，这一点要记住！
{% endnote %}

如果指定了多个配置文件，采取 `last-win` 策略，即「最后获胜侧率」。这句话意思是什么呢，就是说，在你的 `application.properties` 中如果指定了激活好几个配置文件，那么，最后指定的那个配置文件才会生效。

我们分别创建两个环境配置文件：

- `application-dev.properties` 测试环境

```shell
server.port=8081
```

- `application-prod.properties` 生产环境

```shell
#server.port=8082
```

> Spring Boot 的配置文件除了可以使用 properties 文件之外，还支持的 YAML 文件

### 通过 application.prperties 指定 Profile

`applcation.properties` 公共配置文件。激活 Dev 配置文件，需要在 `application.properties` 设置：

```shell
#这里定义8080主要是为了看端口设置是否会被 dev 覆盖
server.port=8080
spring.profiles.active=dev
```

这时候启动程序时，我们查看控制台就可看到 `Dev` 环境被激活了，应用端口是 `8081`。这时候观察 `application.properties` 的端口设置是否会会生效。经过测试可以发现，此时`8080` 端口被 `Dev` 的配置覆盖了。

接着，我们激活 `Prod` 配置，注意，此时我将 `Prod` 端口配置注释掉了，这时候观察 `application.properties` 的端口设置是否会会生效。经过测试可以发现，此时`8080` 端口生效了。

### 通过 Environment 指定 Profile

除了上面在 `application.properties` 指定激活的配置外，还可以在 Envirionment 中设置相关环境变量激活：

```shell
spring.profiles.active=dev
```

经过测试，我在 Environment 中设置环境变量激活了 `Dev` 的配置，然后在 `application.properties` 激活的是 `Prod` 的配置，最终控制台日志显示， `Dev` 配置被激活。

### jar 方式运行时

如果采用 `mvn clean package` 打出 `jar` 包，那么可以使用如下方式指定 Profile：

```shell
java -jar spring-boot-hello-world-0.0.1-SNAPSHOT.jar --spring.profiles.active=dev
```

在命令行方式启动 Spring Boot 应用时，连续的两个减号 `--` 就是对 `application.properties` 中的属性值进行赋值的标识。通过命令行来修改属性值是Spring Boot 非常重要的一个特性，通过此特性，理论上已经使得我们应用的属性在启动前是可变的，所以其中端口号也好、数据库连接也好，都是可以在应用启动时发生改变。

补充：我们有时候利用 `java` 方式启动时，会带参数 `-D`，这个怎么理解呢？

> use -D to define system properties

可以阅读下面两个链接：

- [java程序启动参数-D详解](https://blog.csdn.net/fenglongmiao/article/details/80511512)
- [Why do JVM arguments start with “-D”?](https://stackoverflow.com/questions/44745261/why-do-jvm-arguments-start-with-d)
- [Spring Boot 配置文件中的花样，看这一篇足矣！](https://mp.weixin.qq.com/s?__biz=MzAxODcyNjEzNQ==&mid=2247487568&idx=1&sn=301a4e37018745ce65962fea59d209dd&chksm=9bd0bdc8aca734dec72d546ca1c7f4e6d919524db11a2ac8890a460fc09d7538fdcc15da4f4d&mpshare=1&scene=1&srcid=0611imP7tqE7INs3wcEcTS3A#rd)

### 自定义属性参数

我们可以在 Profile 中指定一些 `propety` 的值，在程序中可以获取到。

在 `application-dev.properties` 定义如下：

```shell
server.port=8081
author.name=Michael
```

接着，我们在 Controller 类中使用 `@Value` 注，即可获取到这个属性：

```shell
@RestController
public class HelloWorld {
    @Value(value = "${author.name}")
    private String authorName;

    @GetMapping("/hello")
    public String sayHello() {
        return "Hello World By " + authorName;
    }

}
```

![property](https://i.bmp.ovh/imgs/2019/07/62f76b56034ce959.jpg)

### 使用随机数

在配置文件中，可以使用 `${random}` 来生成不同类型的随机数。

```shell
book.value = ${random.value}
book.intValue = ${random.int}
book.longValue = ${random.long}
book.uuid = ${random.uuid}
# 1000 以内的随机数
book.randomNumber = ${random.int(1000)}
```

## 热部署

只需要引入 `spring-boot-devtools` 依赖即可。引入依赖之后，重新编译修改的类文件或者配置文件， Spring Boot 框架会自动重启：

```shell
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <scope>runtime</scope>
</dependency>
```

在 IDEA 的顶部菜单栏的 `Build` 中可以看到编译相关的选项。

## 定制 Banner

Banner 就是指我们在启动应用时控制台一开始打印的那个内容，默认是打印 `Spring Boot`。

1. `src/main/resorces` 下新建一个名为 `banner.txt` 的内容；
2. 复制你想显示的内容到 `banner.txt`。

个性化 Banner 来源：

- http://patorjk.com/software/taag
- http://www.network-science.de/ascii/
- https://github.com/Blankj/awesome-comment

送一个有趣的 Banner：

```shell
////////////////////////////////////////////////////////////////////
//                          _ooOoo_                               //
//                         o8888888o                              //
//                         88" . "88                              //
//                         (| ^_^ |)                              //
//                         O\  =  /O                              //
//                      ____/`---'\____                           //
//                    .'  \\|     |//  `.                         //
//                   /  \\|||  :  |||//  \                        //
//                  /  _||||| -:- |||||-  \                       //
//                  |   | \\\  -  /// |   |                       //
//                  | \_|  ''\---/''  |   |                       //
//                  \  .-\__  `-`  ___/-. /                       //
//                ___`. .'  /--.--\  `. . ___                     //
//              ."" '<  `.___\_<|>_/___.'  >'"".                  //
//            | | :  `- \`.;`\ _ /`;.`/ - ` : | |                 //
//            \  \ `-.   \_ __\ /__ _/   .-` /  /                 //
//      ========`-.____`-.___\_____/___.-`____.-'========         //
//                           `=---='                              //
//      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^        //
//            佛祖保佑       永不宕机     永无BUG                  //
////////////////////////////////////////////////////////////////////
```

## 补充知识

### spring-boot-starter

Spring Boot 提供了很多”开箱即用“的依赖模块，都是以 `spring-boot-starter-xx` 作为命名的。下面列举一些常用的模块：

- spring-boot-starter-logging ：使用 Spring Boot 默认的日志框架 Logback。
- spring-boot-starter-log4j ：添加 Log4j 的支持。
- spring-boot-starter-web ：支持 Web 应用开发，包含 Tomcat 和 spring-mvc。
- spring-boot-starter-tomcat ：使用 Spring Boot 默认的 Tomcat 作为应用服务器。
- spring-boot-starter-jetty ：使用 Jetty 而不是默认的 Tomcat 作为应用服务器。
- spring-boot-starter-test ：包含常用的测试所需的依赖，如 JUnit、Hamcrest、Mockito 和 spring-test 等。
- spring-boot-starter-aop ：包含 spring-aop 和 AspectJ 来支持面向切面编程（AOP）。
- spring-boot-starter-security ：包含 spring-security。
- spring-boot-starter-jdbc ：支持使用 JDBC 访问数据库。
- spring-boot-starter-redis ：支持使用 Redis。
- spring-boot-starter-data-mongodb ：包含 spring-data-mongodb 来支持 MongoDB。
- spring-boot-starter-data-jpa ：包含 spring-data-jpa、spring-orm 和 Hibernate 来支持 JPA。
- spring-boot-starter-amqp ：通过 spring-rabbit 支持 AMQP。
- spring-boot-starter-actuator ： 添加适用于生产环境的功能，如性能指标和监测等功能。

### SpringBoot 目录结构

常用目录结构理解：

- controller：控制层，前端控制器，负责页面访问控制，controller 是用来接收页面参数，并且调用逻辑处理，最后组织页面响应的地方。我们不可以在controller 进行逻辑处理，controller 只应该负责用户 API 入口和响应的处理。主要是对外提供的 API 接口，用户使用服务时的入口处，可以结合 swagger 生成对应的 API 文档
- service：业务层，逻辑层，主要是业务类代码，归档了前端控制器中相关服务的操作方法接口类，该文件夹下包含子 impl 文件夹，归档对应的实现接口
- domain：实体类，归档对应的实体（Entity），一个实体尝尝就对应着数据库中一张表
- dao：数据访问层，实体类对应的数据库操作接口类，它与数据库进行交互，封装了对数据库的 CURD 操作
- config：配置信息类
- utils：工具类
- constant：常量接口类

当请求来了，controller 就会将相应的请求分发到相应的 service层，在 service 层中再调用 dao 层进行数据库交互。这里的 dao 层其实就是之前的 model 层，封装了对数据库的操作。这样一来，就把业务处理逻辑从 controller 中分离出来，从而实现了解耦。

### 领域模型

分层领域模型规约，熟悉一下：

- DO（ Data Object）：**与数据库表结构一一对应**，通过 DAO 层向上传输数据源对象。★★★
- DTO（ Data Transfer Object）：数据传输对象，Service 或 Manager 向外传输的对象。★★★
- BO（ Business Object）：业务对象。 由 Service 层输出的封装业务逻辑的对象。
- AO（ Application Object）：应用对象。 在Web层与Service层之间抽象的复用对象模型，极为贴近展示层，复用度不高。
- VO（ View Object）：显示层对象，通常是 Web 向模板渲染引擎层传输的对象。★★★
- POJO（ Plain Ordinary Java Object）：在本手册中， POJO专指只有setter/getter/toString的简单类，包括DO/DTO/BO/VO等。
- Query：数据查询对象，各层接收上层的查询请求。 注意超过2个参数的查询封装，禁止使用Map类来传输。

领域模型命名规约：

- 数据对象：`xxxDO`，xxx即为数据表名。★★★
- 数据传输对象：`xxxDTO`，`xxx` 为业务领域相关的名称。目前项目中，牵扯到和其他周边部署系统需要实体类接受数据时，这样的对象往往以 `DTO` 结尾 ★★★
- 展示对象：`xxxVO`，`xxx` 一般为网页名称。目前项目中 Ctroller 层涉及的请求体，通常以 `VO` 结尾。★★★
- POJO是DO/DTO/BO/VO的统称，禁止命名成xxxPOJO。

## 参考

- [江南一点雨-初识Spring Boot框架](https://blog.csdn.net/u012702547/article/details/53740047)
- [boot-features-external-config-profile-specific-properties](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-external-config-profile-specific-properties) 官宣
- [博客园-静默虚空-Spring Boot 之 Profile 使用](https://www.cnblogs.com/jingmoxukong/p/10151785.html#commentform)
- [InfoQ-胡正林-Spring Boot 多环境配置最佳实践](https://www.infoq.cn/article/Q-ese4CxV2IWmltsJcGX) 偏底层，了解 Profile 激活流程可查阅
- [新年彩蛋：Spring Boot自定义Banner](http://blog.didispace.com/spring-boot-banner/)
- [梁桂钊-Spring Boot 揭秘与实战（一） 快速上手](http://blog.720ui.com/2016/springboot_01_quickstart/) spring-boot-starter

目录结构参考

- [Spring Boot工程结构(推荐)](https://blog.csdn.net/u011659172/article/details/52934206)
- [【系统学习SpringBoot】目录结构（建议）](https://blog.csdn.net/Small_Mouse0/article/details/77826906?utm_source=blogxgwz1)
- [Spring Boot基础(三):Spring Boot项目推荐工程结构](http://blog.longjiazuo.com/archives/1903)
- [使用SpringBoot的推荐项目目录结构](https://www.cnblogs.com/songxingzhu/p/9597927.html)
- [浅谈MVC分层架构中的层次](https://blog.csdn.net/Earl_yuan/article/details/50382431)
- [YezhiweiBlog-Spring Boot 工程结构规范——项目定义级包结构](https://yezsitei.github.io/springboot/2017/10/24/Spring-Boot-%E9%A1%B9%E7%9B%AE%E7%BB%93%E6%9E%84/)
- [博客园-阿里巴巴Java开发手册中的DO、DTO、BO、AO、VO、POJO定义](https://www.cnblogs.com/EasonJim/p/7967999.html)
- [知乎-PO BO VO DTO POJO DAO DO这些Java中的概念分别指一些什么？](https://www.zhihu.com/question/39651928)

Spring Boot 启动原理：

- [老钱-SpringBoot 究竟是如何跑起来的?](https://mp.weixin.qq.com/s?__biz=MzI0MzQyMTYzOQ==&mid=2247484837&idx=1&sn=7e1ae0a453984c7e3bf5a24825de8979&chksm=e96c1d0dde1b941b1117ba5e780d07ae25b2e3d426de87c0e2209b35722541c131650d010365&mpshare=1&scene=1&srcid=0105mKe5g6v4TCdJcYBvBaWv#rd)