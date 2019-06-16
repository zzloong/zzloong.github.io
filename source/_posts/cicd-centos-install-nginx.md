---
title: CentOS Nginx 安装及配置
date: 2019-06-08 10:30:05
categories: DevOps
tags:
  - Nginx
keywords:
  - Nginx
---

## 概念

- [了解 Nginx 的基本概念](http://huang-jerryc.com/2016/10/14/%E4%BA%86%E8%A7%A3%20Nginx%20%E7%9A%84%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5/)

## 安装

```shell
apt-get install nginx # Ubuntu
yum install nginx -y # CentOS
```

<!-- more -->

## 配置文件

```shell
nginx -t # 检查配置文件正确性 test configuration and exit
```

## 主要文件位置

- `which nginx # or whereis nginx`：查看主程序文件
- `/etc/init.d/`：下创建了启动脚本nginx（需手动配置），支持`service nginx start`命令
- `/var/log/nginx`：日志文件夹，`/var/log/nginx/error.log`：错误日志文件，`/var/log/nginx/access.log`：访问日志文件
- `/etc/nginx/nginx.conf`：Nginx全局站点配置文件，日志文件可以在`/etc/nginx/nginx.conf`中配置，默认读取的配置文件
- `/etc/nginx/conf.d`：自定义Nginx站点配置文件存放目录
- `/etc/nginx/conf.d/default.conf`：网站默认站点配置
- `/usr/share/nginx/html`：网站文件默认存放目录
- `sites-available`：则是管理大量站点时服务器的一种通用配置。
- `sites-enabled`：则是一种单独配置，需要使用`enabled`时，需要使用`ln`命令软连接到相应网站。

```
sudo ln -s ~/path/to/your/mysite/mysite_nginx.conf /etc/nginx/sites-enabled/
```

`/etc/nginx/nginx.conf`中有如下语句：
```
	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
```
可见，配置文件放在`sites-avaliable`文件夹中，需要创建软连接在`sites-enabled`中，才会生效。

`server_name`的作用其实是当该机器上同时部署了其他域名服务时起作用的。

> 如果nginx中只配置一个server域的话，则nginx是不会去进行server_name的匹配的。因为只有一个server域，也就是这有一个虚拟主机，那么肯定是发送到该nginx的所有请求均是要转发到这一个域的，即便做一次匹配也是没有用的。还不如干脆直接就省了。如果一个http域的server域有多个，nginx才会根据$hostname去匹配server_name进而把请求转发到匹配的server域中。此时的匹配会按照匹配的优先级进行，一旦匹配成功进不会再进行匹配

参考：
- [nginx server_name怎么可有可无](https://www.oschina.net/question/922543_91357)


`location`接受两个参数，一个字符串或者正则，和一段代码。字符串用于匹配某个特定目录。

```
     location / {
         autoindex on;
         autoindex_exact_size off; # 默认是开，以b为单位，关闭后，会显示MB GB
```

### 注意点
nginx 默认的账号权限太低，没有部分文件的访问权限，导致访问时出现 `403 Forbidden`。

配置文件 `/etc/nginx/nginx.conf`：
```
user  nginx;
```

应该修改为：
```
user root;
```

## Nginx运行
```
/usr/sbin/nginx -h # 查看帮助
```

```
/usr/sbin/nginx -c /etc/nginx/nginx.conf # 默认也会读取该配置文件，所以，可以不加-c参数
nginx # 运行/usr/sbin目录下的nginx的命令
nginx -s reload # 运行这句话的前提是，nginx正在运行，重载，如果之前运行了stop，那么会提示出错  "/run/nginx.pid" failed
service nginx start
service nginx status
```

## FAQ
### Q1：nginx: [error] open() "/usr/local/var/run/nginx.pid" failed (2: No such file or directory)

- [nginx 重启报错:nginx](https://www.jianshu.com/p/4f8b57632e2b)

### Q2：中文乱码

Nginx的server的配置内容，增加一行：`charset utf-8;`

- [Nginx 显示中文乱码解决](https://blog.csdn.net/qq_35448976/article/details/79256873)

### Q3：权限问题导致Nginx 403 Forbidden错误的解决方法
- [权限问题导致Nginx 403 Forbidden错误的解决方法](https://www.jb51.net/article/54190.htm)

## 参考
- [写给Web开发人员看的Nginx介绍](https://fraserxu.me/2013/06/22/Nginx-for-developers/)
- [nginx配置初探](http://blog.qiji.tech/archives/2425)
- [nginx配置文件参数详解（完整版）](https://codeday.me/collect/20181015/307342.html)
- [Nginx浏览目录配置及美化](https://segmentfault.com/a/1190000012606305)