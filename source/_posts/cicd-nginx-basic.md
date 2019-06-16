---
title: Nginx 基础知识学习
date: 2019-06-08 10:21:35
categories: DevOps
tags:
  - Nginx
keywords:
  - Nginx
---

## 资料

### 基础资料

- [掘金-前端开发者必备的Nginx知识](https://juejin.im/post/5c85a64d6fb9a04a0e2e038c) 介绍的比较综合，正向代理反向代理的区别、负载均衡等知识，都有介绍
- [静默虚空-Nginx 简易教程](https://www.cnblogs.com/jingmoxukong/p/5945200.html#!comments) 博客园上的一篇推荐文章
- [简书-全面了解Nginx到底能做什么](https://www.jianshu.com/p/8bf73d1a758c)
- [Nginx的负载均衡 - 加权轮询 (Weighted Round Robin) 上篇](https://blog.csdn.net/zhangskd/article/details/50194069)，这个介绍了`upstream`资源池的调度算法之一，在其[专栏](https://blog.csdn.net/column/details/sknginx.html)还有其他介绍。
- [平滑的基于权重的轮询算法](https://colobu.com/2016/12/04/smooth-weighted-round-robin-algorithm/) ：本文也是介绍资源池调度算法的
- [理解nginx的配置](https://www.cnblogs.com/52fhy/p/5054516.html#commentform)：介绍的比较全面的一篇文章
- [Nginx实战（五） 反向代理](https://zhuanlan.zhihu.com/p/35858793)
- [Understanding Nginx HTTP Proxying, Load Balancing, Buffering, and Caching](https://www.digitalocean.com/community/tutorials/understanding-nginx-http-proxying-load-balancing-buffering-and-caching)
- [13 Nginx Location Directive Examples including Regular Expression Modifiers](https://www.thegeekstuff.com/2017/05/nginx-location-examples/) 关于`location`的作用介绍，很全面
- [Nginx的Upstream来做负载 部署两台WEB](https://zhuanlan.zhihu.com/p/34415506) ：这个文章虽然比较少，但是里边明确了`upstream`名称和`proxy_pass`配置的对应关系。
- [proxy_pass的小说明](https://segmentfault.com/a/1190000008061457)：关于proxy_pass的一些FAQ

### Nginx 性能优化

- [百万并发下 Nginx 的优化之道](https://mp.weixin.qq.com/s?__biz=MzA4Nzg5Nzc5OA==&mid=2651674303&idx=1&sn=b34cbf009aac5f901ce81a65df06359f&chksm=8bcb9516bcbc1c006ff36cd7a8a83d40c7b24143d0c76fc15f61bbb2c210355615125aa95d94&mpshare=1&scene=1&srcid=1031DJIgeaaQj5fafJbAc3mg#rd)
- [agentzh 的 Nginx 教程](https://openresty.org/download/agentzh-nginx-tutorials-zhcn.html)

## 常用命令

````shell
nginx -s stop       快速关闭Nginx，可能不保存相关信息，并迅速终止web服务。
nginx -s quit       平稳关闭Nginx，保存相关信息，有安排的结束web服务。
nginx -s reload     因改变了Nginx相关配置，需要重新加载配置而重载。
nginx -s reopen     重新打开日志文件。
nginx -c filename   为 Nginx 指定一个配置文件，来代替缺省的。
nginx -t            不运行，而仅仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。
nginx -v            显示 nginx 的版本。
nginx -V            显示 nginx 的版本，编译器版本和配置参数。
```

## 反向代理

> 将server节点下的location节点中的proxy_pass配置为：http:// + upstream名称

app.conf：
```
upstream micahel-machine-manager {
    server 100.253.128.222:40012;
    server 100.253.128.223:40012;
}

upstream michael-machine-server {
    server 100.253.128.55:40020;
    server 100.120.128.56:40020;
}

server {
    listen 80;
    server_name michael-api.hw.com;

    client_max_body_size 20M;
    client_body_buffer_size 10M;
    proxy_redirect     off;
    proxy_set_header   Host             $host;
    proxy_set_header   X-Real-IP        $remote_addr;
    proxy_next_upstream error timeout invalid_header http_502 http_503 http_504;
    proxy_ignore_client_abort on;
    proxy_read_timeout  180;
    proxy_buffering on;
    proxy_buffer_size 8k;

    proxy_buffers 8 8M;

    gzip                on;
    gzip_min_length     1000;
    gzip_types          text/plain text/css application/json text/xml application/xml application/xml+rss text/javascript;

    location /MachineManager/ {
        proxy_pass http://michael-machine-manager;
    }

    location /MachineServer/ {
        proxy_pass http://michael-machine-server;
    }


    access_log /var/log/nginx/cid.hw.com-access.log main;
}
```

如何配置 Nginx 呢？可以利用这个神器：
- [NginxConfig](https://nginxconfig.io/)
