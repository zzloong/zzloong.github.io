---
title: hexo之github\gitcafe多线路托管博客，DNS域名设置
tags: [Hexo, DNS]
toc: true
date: 2015-11-30 14:35:06
categories: Tools
---
上一篇[挖坑总结（1）](http://michaelxiang.me/2015/11/30/hexo-gitcafe-github-deploy/)中已经详细讲解了同时部署到gitcafe和github上的方法了，那么，接下来
要讲解的就是DNS域名相关的问题：
# 在goddaddy上购买域名
关于goddaddy购买域名及设置的流程，就不再说了，很简单，可以参考：[Godaddy优惠码网站](http://www.dute.me/guideline/)

<!-- more -->
# 设置github及gitcafe

## github
要在博客站点`source`文件夹，如：`E:\KP\my_blog\source`下新建CNAME文件，编辑加入：
`michaelxiang.me`
github官方参考：[Categories / GitHub Pages Basics](https://help.github.com/categories/github-pages-basics/)

## gitcafe
在gitcafe的page设置，添加域名:
`michaelxiang.me`
gitcafe官方参考：[gitcafe关于page说明](https://help.gitcafe.com/manuals/help/pages-services)

# Dnspod域名解析设置

## github相关
想要访问[michaelxiang.me](michelxiang.me)是通过github访问，我们在dnspod设置完CNAME就够了。

## gitcafe相关
在dnspod里设置，为了让国内ip访问[michaelxiang.me](michelxiang.me)是通过gitcafe，我们需要设置CNAME。
![dnspod设置图](http://i5.tietuku.com/7d4e84f088f8514b.png)

说明：
+ 这里建议用设置CNAME的方式，为了防止github或者gitcafe主机迁移导致A记录的ip也需要更改才能访问的额弊病。
+ gitcafe的记录值，`gitcafe.io`，而不是`用户名+gitcafe.io`,这和github不一样，归功于gitcafe提供的域名绑定功能！
+ 主机记录是`@`,没用其他的，这样设置就OK了，因为我自己用的是裸域名。[Hexo多Repo部署——使用Github和GitCafe同时托管博客](http://www.alok2333.com/2015/10/03/blog-hexo-next/#comment-2383478967)
+ 线路类型，gitcafe对应的是`国内`,github对应的是`默认`，这么设置的话，那么国内ip访问就是访问的gitcafe，国外ip就是github。
+ 如下图，可以设置监控，当有问题时，切换解析。
![监控](http://i5.tietuku.com/21b9ca72e190ff68.png)

## 测试
```
ping michaelxiang.me
```
我们将会看到，它的实际访问ip，就是gitcafe.io。
![ping测试](http://i5.tietuku.com/9977e4eedd3e0f55.png)
懂点ping方面的知识也是不错的，也算是这次搭建hexo博客的收获之一。
+ 经过比较，发现，gitcafe的博客访问速度确实要快一点。
+ 听过ping可以发现如果是要增加A记录的话，ip地址该写多少。[通过dnspod解析域名](http://www.alok2333.com/2015/10/03/blog-hexo-next/#comment-2384631807)
+ 已发送若是大于已接收的话，就代表发生了`丢包`的现象
+ 通过ping+网址\ip 地址，可以查看主机运行情况


#参考

[关于CNAME介绍参考](http://www.zhihu.com/question/20414602)
[dnspod官方参考](https://support.dnspod.cn/Kb/showarticle/tsid/32/)

----

