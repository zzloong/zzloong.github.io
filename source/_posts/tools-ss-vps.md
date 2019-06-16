---
title: shadowsocks+vps+mac
date: 2019-04-27 14:21:24
tags: [ss,vps]
categories: ToolsDaily
keywords:
---

Vultr 选择的是日本的节点，发现速度比较不错，油管视频 720P 无压力！而且，感觉 Vultr 的界面也很清新，用着很方便。

![](https://ws3.sinaimg.cn/large/006tNbRwly1fyh2t0f4hgj328o0qcdpb.jpg)


<!-- more -->

我自搭梯子，选择的是：`Shadowsocks-libev+开启simple-obs插键+BBR加速`。

Vultr 通过我的链接注册充值，你我都可以获得 $10 账户返利，所以，如果有需要，就点击注册吧：
- [Vultr  注册返利链接](https://www.vultr.com/?ref=7488919)

## VPS

vultr速度比较：
- [vultr-downloadspeedtests](https://www.vultr.com/faq/#downloadspeedtests)
- [vultr中文网](https://www.thevultr.org/)

## 一键安装脚本

- [秋水逸冰 Shadowsocks 一键安装脚本（四合一）](https://teddysun.com/486.html)
- [秋水逸冰 一键脚本搭建SS/搭建SSR服务并开启BBR加速](https://teddysun.com/489.html)
- [Vultr中文网-Vultr 一键搭建酸酸 Shad0ws0cks 图文教程（推荐）](https://www.vultrcn.com/6.html)

```
wget --no-check-certificate -O shadowsocks-all.sh https://raw.githubusercontent.com/teddysun/shadowsocks_install/master/shadowsocks-all.sh
chmod +x shadowsocks-all.sh
./shadowsocks-all.sh 2>&1 | tee shadowsocks-all.log
```

Docker方式

- [docker文档](https://docs.docker.com/install/linux/docker-ce/centos/#install-using-the-repository)
- [docker-ss-搜索结果](https://hub.docker.com/search/?isAutomated=0&isOfficial=0&page=1&pullCount=1&q=shadowsocks&starCount=0)

## 加速配置：

我选用的 BBR 加速的方式

- BBR 安装：

```
wget --no-check-certificate https://github.com/teddysun/across/raw/master/bbr.sh && chmod +x bbr.sh && ./bbr.sh
```

- 锐速安装，参考[锐速破解版linux一键自动安装包](https://github.com/91yun/serverspeeder)：

```
wget -N --no-check-certificate https://github.com/91yun/serverspeeder/raw/master/serverspeeder.sh && bash serverspeeder.sh
```

## 客户端

- [Shadowsocks各种客户端](https://shadowsocks.org/en/download/clients.html)

Mac 客户端下载
- [ShadowsocksX-NG-R/releases](https://github.com/qinyuhang/ShadowsocksX-NG-R/releases)
- [ShadowsocksX-NG/releases](https://github.com/shadowsocks/ShadowsocksX-NG/releases)
- [NG 客户端插键配置](https://github.com/shadowsocks/ShadowsocksX-NG/wiki/SIP003-Plugin)

![](https://ws3.sinaimg.cn/large/006tNc79ly1g2h75w3g2aj30ff08wq2y.jpg)

插键选项：

```
obfs=http;obfs-host=www.bing.com
```

Android
- [shadowsocks/shadowsocks-android](https://github.com/shadowsocks/shadowsocks-android/releases)
- [插键-shadowsocks/simple-obfs-android](https://github.com/shadowsocks/simple-obfs-android/releases)

## 服务端命令

启动脚本后面的参数含义，从左至右依次为：启动，停止，重启，查看状态。

- Shadowsocks-Python 版：`/etc/init.d/shadowsocks-python start | stop | restart | status`
- ShadowsocksR 版：`/etc/init.d/shadowsocks-r start | stop | restart | status`
- Shadowsocks-Go 版：`/etc/init.d/shadowsocks-go start | stop | restart | status`
- Shadowsocks-libev 版：`/etc/init.d/shadowsocks-libev start | stop | restart | status`

配置文件：
```
Shadowsocks-Python 版：
/etc/shadowsocks-python/config.json

ShadowsocksR 版：
/etc/shadowsocks-r/config.json

Shadowsocks-Go 版：
/etc/shadowsocks-go/config.json

Shadowsocks-libev 版：
/etc/shadowsocks-libev/config.json
```

## 参考
- [聪聪-SS/SSR 简介](https://congcong0806.github.io/2018/04/20/SS/)
- [写给非专业人士看的 Shadowsocks 简介](http://vc2tea.com/whats-shadowsocks/)
- [VPS+ShadowsocksR 搭建自己的 VPN](https://www.liaoyuqin.com/post/tools/ha-bi-da-ti-zi)