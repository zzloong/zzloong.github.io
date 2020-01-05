---
title: Maven 基础知识
date: 2020-01-05 21:49:10
tags:
  - Java
  - Maven
categories:
  - Java
---


![Maven](https://gitee.com/michael_xiang/images/raw/master/dLVeb2.jpg)

## 简介

Maven是Java项目构建工具，可以用于管理Java依赖，还可以用于编译、打包以及发布Java项目，类似于JavaScript生态系统中的NPM。

<!-- more -->

构建环节：`清理-编译-测试-报告-打包-部署`

- 清理：将编译代码前生成的内容删除
- 编译：将源代码编译为字节码
- 测试：运行单元测试用例
- 报告：测试程序的结果
- 打包：将 java 项目打成 java 包；将 Web 项目达成 war 包；
- 安装：将 jar 或 war 生成到 Maven 仓库中；
- 部署：将 jar 或 war 从 Maven 仓库中部署到 Web 服务器上运行；

## 安装maven

[官方安装包](https://maven.apache.org/download.cgi)，直接下载二进制包，我使用的是 `apach-maven-3.6.2-bin.tar.gz`

```
weget http://mirrors.tuna.tsinghua.edu.cn/apache/maven/maven-3/3.6.2/binaries/apache-maven-3.6.2-bin.tar.gz
tar xvf apache-maven-3.6.2-bin.tar.gz -C ~/opt
echo "export PATH=$PATH:/Users/michael/opt/apache-maven-3.6.2/bin" >> ~/.zshrc
source ~/.zshrc
```

到此，Mac 环境的 maven 的就已经安装好了，检测：

```
mvn --version
```

来源 [my-config-files/maven/](https://github.com/Michael728/my-config-files/tree/master/maven)

## 配置相关

### settings.xml文件

可以定义本地仓库的实际路径：

```shell
<localRepository>/path/to/local/repo</localRepository>
```

Maven 镜像源设置，第一个 mirror 才会生效，其他的只是放这儿，后期可以将某个调整至第一个，使其生效，提高下载速度和稳定性：

```
	<!-- https://blog.csdn.net/sayyy/article/details/80447757 -->
    <mirror>
      <!--This sends everything else to /public -->
      <id>nexus</id>
      <mirrorOf>*</mirrorOf>
      <url>https://maven.aliyun.com/nexus/content/groups/public/</url>
    </mirror>
    <mirror>
      <!--This is used to direct the public snapshots repo in the
          profile below over to a different nexus group -->
      <id>nexus-public-snapshots</id>
      <mirrorOf>public-snapshots</mirrorOf>
      <url>https://maven.aliyun.com/nexus/content/repositories/snapshots/</url>
    </mirror>
<!-- https://mirrors.huaweicloud.com/ -->
<!-- https://bbs.huaweicloud.com/forum/forum.php?mod=viewthread&tid=1779 -->
    <mirror>
        <id>huaweicloud</id>
        <mirrorOf>*</mirrorOf>
        <url>https://mirrors.huaweicloud.com/repository/maven/</url>
    </mirror>
```

注：`< mirrorOf>`可以设置为哪个中央仓库做镜像，为名为“central”的中央仓库做镜像，写作`< mirrorOf>central< /mirrorOf>`;为所有中央仓库做镜像，写作`< mirrorOf>*< /mirrorOf>`。Maven默认中央仓库的id 为 central。id是唯一的。 

重要：除非你有把握，否则不建议使用`< mirrorOf>*< /mirrorOf>`的方式。

![](https://ws4.sinaimg.cn/large/006tNbRwly1fyq48ytbe9j30r8085t9z.jpg)

![](https://ws4.sinaimg.cn/large/006tNbRwly1fyq49ym1mqj30ls08zdhe.jpg)

参考：

- [IntellJ IDEA配置Maven以及修改默认Repository](https://zhuanlan.zhihu.com/p/28133184)

### pom.xml

`pom.xml`中，`<project></project>`为最外层的标签；

`<modelVersion>4.0.0</modelVersion>`定义了所使用的POM版本。这2个标签基本上是不变的。

`groupId`、`artifactId` 与 `version` 一起则定义了模块的坐标( `Coordinates` )，每个公共模块的坐标应该是唯一的：

- `groupId`：组织名称，通常是把域名反过来，例如 `com.fundebug`
- `artifactId`：模块名称，比如一个微服务的项目名称，例如 `fundebug-java`
- `version`：模块版本，例如 `0.2.0`
  - 通常项目版本号分为 3 段，`主版本号.次版本号.修订版本号`
  - `SNAPSHORT` 表示开发中的版本；
  - `RELEASE` 表示一个正式发布版本，也可能没有任何后缀也表示正式版
  - `M1 M2 ...`  M 表示里程碑，即将发布；
  - `RC( Release Candicate)` 发布候选
  - `GA(General availability)` 基本可用版本
  - `SNAPSHORT<M1<M2<...<RC<GA<RELEASE`

[SpringBoot Star](thttps://start.spring.io/) 中可以看到版本的一个示例：

![](https://ws3.sinaimg.cn/large/006tNc79gy1g1rwybm9ijj30p40a1aaw.jpg)

`<dependencies></dependencies>`定义了当前项目所依赖的模块。Maven 可以根据 `<dependency></dependency>` 中定义的坐标，自动下载所依赖的模块。在 MacBook 上，Maven 将下载的模块缓存在 `$HOME/.m2/` 目录。

参考：
- [fundebug-Maven入门教程](https://blog.fundebug.com/2019/01/07/maven-tutorial/) 推荐

## Maven 常用命令

- `mvn compile` 编译 Maven 工程，生成一些 `class` 文件和配置文件；
- `mvn package` 编译并打包，根据 `pom.xml` 中元素 `packaging` 是 `jar` 还是 `war` 进行打包。
- `mvn install` 打包并安装到本地仓库。比如 `env-service` 服务可以安装到本地，`deploy-service` 服务可以通过项目在本地引用到；
- `mvn deploy` 同 `install`，但打包并安装到远程仓库；
- `mvn clean` 删除 target 目录；

最近项目中用来打包生成 `jar` 包的命令：

```shell
mvn clean package
```

会在同目录下生成一个 `target` 目录，`jar` 包就在该子目录下；

同时，另外一个项目是 gradle 写的，类似命令：

```shell
gradle -x test build
```

跳过测试阶段，生成的 jar 包在 build 的子目录中；

- [Gradle vs Maven](https://www.journaldev.com/8396/gradle-vs-maven) 推荐