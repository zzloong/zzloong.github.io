---
title: CentOS 安装 CICD 利器 Jenkins
date: 2018-10-11 23:09:45
tags:
  - CICD
  - DevOps
  - Jenkins
categories: [DevOps]
---

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1fw72wbene1j20go09eqb4.jpg)

## 环境准备

### 安装JDK

#### 安装Oracle JDK

<!-- more -->

Jenkins 自身采用 Java 开发，所以要必须安装 JDK；
- [博客园-Linux安装JDK](https://www.cnblogs.com/michael-xiang/p/10467749.html)

```shell
export JAVA_HOME=/usr/java/jdk1.8.0_171
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
```

创建软连接：

```
ln -s /usr/local/java/jdk1.8.0_171/bin/java /usr/bin/java
```

注意：Java版本不能是`gcj`，会导致Jenkins有问题，[centos7搭建jenkins小记](https://segmentfault.com/a/1190000007086764)文章中提到的java版本问题导致CentOS下的Jenkins有问题。

#### 安装`openjdk`的方法


```
yum -y install java-1.8.0-openjdk java-1.8.0-openjdk-devel
```

参考：
- [Centos7安装OpenJDK8](https://blog.csdn.net/kanbe_kotori/article/details/70948430)
- [OpenJDK官网](http://openjdk.java.net/install/)

### 安装Git

```
yum install git
```

### 规避磁盘过满问题

1.方法1：创建软连接，准备较大空间

`/data`挂在了较大空间，然后在其中创建相关目录：

```
mkdir -p /data/jenkins
ln -s /data/jenkins/ /var/lib/jenkins
```

2.方法2：丢弃构建

设置构建最大保留天数或者保留个数，实现自动删除构建结果。

参考：
- [Jenkins服务器磁盘空间管理策略](https://blog.csdn.net/lantian08251/article/details/41380483)

## CentOS

### 离线安装-推荐

```
## http://pkg.jenkins-ci.org/redhat-stable/
wget http://pkg.jenkins-ci.org/redhat/jenkins-2.39-1.1.noarch.rpm ## 下载(也可以Windows下载再转过来)
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key ## 导入公钥，发现离线安装，不需要导入公钥就能安装
rpm -ih jenkins-2.7.2-1.1.noarch.rpm
```

自动安装完成之后：

- `/usr/lib/jenkins/jenkins.war`    WAR包
- `/etc/sysconfig/jenkins`       配置文件
- `/var/lib/jenkins/`        默认的JENKINS_HOME目录
- `/var/log/jenkins/jenkins.log`    Jenkins日志文件

启动Jenkins:

```
sudo systemctl enable jenkins # 开机自启动Jenkins
sudo systemctl start jenkins # 启动Jenkins
```

查看服务细节：

```
systemctl status jenkins.service
```

验证Jenkins Server访问链接：

```
telnet <ip> 8080
```

如果访问有问题，需要把防火墙关了：

```
systemctl stop firewalld
systemctl disable firewalld.service #重启不自动开启
```

通过如下两个命令查看防火墙是否关闭：

```
systemctl list-unit-files|grep firewalld.service
iptables -t nat -S
```

### 在线安装

```
# 添加Jenkins源
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key
yum repolist # Update your package manager list to get the latest packages
# 安装
yum install java-1.8.0-openjdk jenkins
service jenkins start # 启动
```

### 基于Tomcat安装

- 在[Tomcat官网](https://tomcat.apache.org/download-90.cgi)下载tar包
- 将tar包拷贝至`/usr/local`目录并解压：`cd /usr/local/ && tar zxvf apache-tomcat-9.0.12.tar.gz`
- 访问[jenkins官网](http://pkg.jenkins-ci.org/redhat-stable/)下载长期维护版本的`jenkins.war`拷贝至`/usr/local/apache-tomcat-9.0.12/webapps`，启动tomcat：`cd /usr/local/apache-tomcat-9.0.12`
- 访问：`http://ip:8080/jenkins`

管理员密码访问：
```
cat /root/.jenkins/secrets/initialAdminPassword
```

基于这种方式安装的Jenkins位置处于`/root/.jenkins`，因此，为了避免Job占用空间过大，需要执行如下命令创建软连接：

```
ln -s /data/jenkins_jobs/ /root/.jenkins/jobs
```

### Jenkins设置

为了不因为权限出现各种问题，这里直接使用root

```
## sudo vim /etc/sysconfig/jenkins
JENKINS_USER="root" ## 原值 "jenkins" 必须修改，否则权限不足
JENKINS_PORT="8080" ## 原值 "8080" 可以不修改
```

修改目录权限

```
chown -R root:root /var/lib/jenkins
chown -R root:root /var/cache/jenkins
chown -R root:root /var/log/jenkins
```

### Centos安装参考：

- [How to install Jenkins on CentOS 7](https://www.hugeserver.com/kb/how-install-jenkins-centos7/)
- [Jenkins 持续集成综合实战](https://blog.csdn.net/kefengwang/article/details/54233584)
- [CentOS 7 安装 Jenkins-提到配置权限](https://www.cnblogs.com/stulzq/p/9291237.html)
- [以root用户运行jenkins中shell命令](https://blog.csdn.net/wzqnls/article/details/78506149)
- [CentOS 安装 Jenkins](https://segmentfault.com/a/1190000004639325)
- [ken的杂谈-CentOS 7 下Jenkins安装部署教程](https://ken.io/note/centos7-jenkins-install-tutorial)

## Ubuntu

- 下载`https://pkg.jenkins.io/debian/jenkins.io.key`
- `apt-key add jenkins.io.key`

以上两步，可以合二为一：

```
wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
```

- `sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'`
- `apt-get update`
- `apt-get install jenkins`
- `/etc/init.d/jenkins start`。或者`service  start jenkins`。启动Jenkins。
- 通过：`http://{ip}:8080` 访问jenkins。

`cat /var/lib/jenkins/secrets/initialAdminPassword`得到默认密码填入下面的密码框中

安装好之后，可以修改相关配置改变默认端口：
- `/etc/default/jenkins`

### Ubuntu安装参考：

- [how to change port number for Jenkins installation In Ubuntu 12.04](https://stackoverflow.com/questions/28340877/how-to-change-port-number-for-jenkins-installation-in-ubuntu-12-04)
- [How to install Jenkins on Ubuntu 16.04](https://www.hugeserver.com/kb/install-jenkins-ubuntu-16-04/)

## Jenkins master优化

### 增加同时打开文件句柄数
增加同时打开文件句柄数，linux默认一个进程能同时打开的文件句柄是1024个，在jenkins master肯定是不够的，需要调整成65535

CentOS系统，修改`/etc/security/limits.conf`，在文件最后增加一行:

```
root             -       nofile          65535
```

重启后生效，可以通过命令`ulimit -a`查看

### 设置tomcat 日志循环，限制日志的大小

https://stackoverflow.com/questions/8342336/how-to-set-maximum-number-of-rolls-and-maximum-log-size-for-tomcat

## Jenkisn配置

### Jenkins用户

admin账号初始密码：

```
cat /var/lib/jenkins/secrets/initialAdminPassword
```

- [Jenkins使用 -- 用户设置](https://www.jianshu.com/p/f1d378596a67)

## Jenkins使用

### Jenkins关闭、重启、重载

`http://localhost:8080/[exit/restart/reload]`


参考：
- [Jenkins的关闭、重启](https://blog.csdn.net/itfootball/article/details/44876517)
- [Ubuntu 16安装、配置Jenkins实践](https://www.jianshu.com/p/7625d174c327)

### Jenkins Job指定执行节点

- 关联job，进入job的配置页面，勾选`Restrict where this project can be run`

利用了标签的功能

参考：

- [Jenkins使用教程之管理节点](https://www.jianshu.com/p/047362b11403)

## Jenkins升级

可以通过系统管理(System management)--> 系统信息(System Info)查找`.war`的文件

```
executable-war	/usr/lib/jenkins/jenkins.war
```

先列出官网地址：https://jenkins.io/download/

升级之前，停止Jenkins服务（记得备份原来的jenkins.war，以防万一）

```
http://jenkinsIP:port/exit
```

从官网下载最新的war包，然后替换掉上面路径下的war。

替换完成后，重启：

```
systecmctl start jenkins # centos
service jenkins start # ubunutu
```

参考：
- [Jenkins技巧：如何更新Jenkins到最新版本](https://www.cnblogs.com/dzblog/p/6962000.html)

## Jenkins插件

- dashboard view：[Dashboard-view自定义jenkins任务集视图](https://blog.csdn.net/Anlegor/article/details/44023879)
- Workspace Cleanup Plugin：这个插件可以再每次build之前清空workspace
- Monitoring：监控一些机器的状态信息了
- Folders View：新建任务时，支持创建文件夹。
- SSH Slaves：添加节点时，可以采用SSH方式链接；
- PostBuildScript：根据 Build 状态执行脚本
- Post Build Task
- Pipeline：必须 【请勾选】
- Build Pipeline Plugin：用于创建pipline视图
- Configuration Slicing：主要功能是可以批量设置job的属性，比如设置保留多少天的构建记录，神器之一，有效解决Jenkins磁盘过满的问题
- Multijob plugin：配置Multijob必备的插件；
- Timestamper
- [Build Timeout plugin](http://www.cnblogs.com/itech/p/5694728.html)：构建超时插件
- Folders：可嵌套地定义文件夹来级别 views / jobs
- Custom Tools Plugin
- Git plugin：Git插件 【请勾选】
- Git Parameter
- GitLab
- Gitlab Hook Plugin
- Gitlab Authentication plugin
- Node and Label parameter plugin
- Publish Over SSH
- Groovy plugins：并发任务解决
- Windows Slaves Plugin：连接Windows Slaves，默认安装了
- Matrix Authorization Strategy Plugin：矩阵形式认证策略插件，默认安装了

### 插件使用参考：

- [jenkins常用一些插件](https://blog.csdn.net/hqzxsc2006/article/details/52087876)
- [thoughtworks-第二话：Jenkins必备插件安装](https://github.com/ThoughtWorks-Chengdu-DevOps-Club/tw_devops_workshop/wiki/%E7%AC%AC%E4%BA%8C%E8%AF%9D%EF%BC%9AJenkins%E5%BF%85%E5%A4%87%E6%8F%92%E4%BB%B6%E5%AE%89%E8%A3%85)
- [Jenkins常用插件介绍](http://www.yunxiaobai.net/archives/15)
- [小团队持续集成之实践【6】 - Jenkins - 安装与插件篇](https://www.jianshu.com/p/b50e679e2409)
- [Jenkins持续集成平台搭建](http://shadow000902.space/2017/08/16/Jenkins%E6%8C%81%E7%BB%AD%E9%9B%86%E6%88%90%E5%B9%B3%E5%8F%B0%E6%90%AD%E5%BB%BA/)

## Jenkins Slave

Slave机器上需要安装好git、配置好Java环境（尤其是一些类似JAVA_HOME的变量）。

### 通过SSH连接node--推荐

- 先安装`SSH Slaves`插件，这样在新增节点时，ssh配置将更加友好。
- 点击 `Credentials`， 点击 `Jenkins` -> `Global credentials` -> `Add Credentials`，`Username` 和 `Password` 表示执行机的账号和密码。
- 新建节点，「启动方式」 选择 `Launch slave agents via SSH`。`Credentials` 选择刚刚新建的钥匙，`Host Key Verification Strategy` 选择 `Manually trusted Key Verification Stragegy`。

最后`Launch agent`即可；

### 通过JNLP连接node

该方法比较麻烦，不详细介绍了。

注意：需要下载两个文件到agent机器上：
- `agent.jar`
- `slave-agent.jnlp`

参考：
- [两种常见挂载Jenkins slave节点的方法](https://blog.csdn.net/liuchunming033/article/details/52025541)
- [Jenkins : 安装 master 和 slave](https://www.cnblogs.com/sparkdev/p/7102622.html)

## Jenkins FAQ

### Q1: 忽略Jenkins升级提醒

对于轻微强迫症的我来说，看着升级提醒，还不能叉掉，实在忍不了，Google之后，有解决方法：

```
Manage Jenkins => Configure System => Administrative monitors configuration
系统管理-》系统设置-》管理监控配置
去掉“Jenkins更新通知”
UnCheck "Jenkins Update Notification" and apply
```
- [How to disable displaying “New version of Jenkins (2.62) is available for download (changelog)”?](https://stackoverflow.com/questions/44142638/how-to-disable-displaying-new-version-of-jenkins-2-62-is-available-for-downlo)


### Q2: Cannot run program "java": error=2, No such file or directory

虽然登录到master机器或者执行机上，`java`都配置好了，但是仍然遇到了错误，后来看到一篇博客中提到的方法，在`usr/bin`下创建了一个Java的软连接，就解决了。

```
ln -s /usr/java/jdk1.8.0_171/bin/java /usr/bin
```

- [enkins cannot run program mvn error 2 - No such file or directory Posted by Echo Yuan on July 2](http://echoyuan.me/2017/07/29/jenkins-cannot-run-program-mvn-error-2-no-such-file-or-directory/)

### Q3: jenkins.JenkinsException: Error in request. Possibly authentication failed [500]: Internal Server Error

在JJB项目中，发生了上面的错误，定位问题了半天，发现可能是Jenkins本身出问题了，而不是项目出问题。以前运行OK的Jenkins怎么突然发生这个问题了呢？可能是如下原因造成：

- `var/lib/jenkins/jobs`：目录下Job占据过多空间。**在Jenkins`中的节点管理里查看master机器的`剩余磁盘空间`也可以观察到**。

```
du -ah --max-depth=1
```

`/etc/rc.local`是啥？

参考：

- [【Jenkins学习 】解决jenkins运行磁盘满的问题](https://blog.csdn.net/ouyang_peng/article/details/79225993)

### Q4: starting jenkins bash /usr/bin/java permission denied

解决办法主要分了两步：
1. 将原本位于`/root/buildbox/javaxxx/`下的目录移到了`/usr/java/`下，重新配置java环境；
2. 在`vim /etc/init.d/jenkins`中的`candidates`字段补充上java路径`/usr/java/jdk1.8.0_151/bin/java`。

参考：

- [Jenkins在Linux下的安装与配置](https://my.oschina.net/aibinxiao/blog/1576678)
- [启动jenkins服务错误](https://www.cnblogs.com/jwentest/p/8206831.html)
- [Jenkins fails when running “service start jenkins”](https://stackoverflow.com/questions/39621263/jenkins-fails-when-running-service-start-jenkins)

### Q5: 执行Jenkins服务器界面运行一段时间后就会卡死，界面显示空白

原因：tomcat的日志（/home/apache-tomcat-8.0.30/logs）查看catalina.out日志，得知是java虚拟机内存空间溢出。
解决：到后台tomcat的bin目录下，修改 tomcat的启动脚本：startup.sh，在最上面添加 `export JAVA_OPTS=“-XX:MaxPermSize=1024m -Xms1024m -Xmx3096m”`

### Q6: 构建完成后需要清除workspace
需要安装“Workspace Cleanup Plugin插件”，在构建任务配置时，可以在“增加构建后操作步骤”中选择`Delete workspace when build is done`

### Q7: Linux环境变量问题，没有加载/etc/profile文件，获取不到需要的环境变量。

添加节点时，`Prefix Start Slave Command` 选择输入 `source /etc/profile && `

加一个前置动作。注意`&&`之后要有空格！！！

### Q8：Jenkins定时构建用法

[Jenkins定时构建](https://blog.csdn.net/ezreal_tao/article/details/80959166)

## 原文链接

- [CICD-Jenkins搭建笔记一](https://michael728.github.io/2018/10/11/cicd-jenkins-01/)