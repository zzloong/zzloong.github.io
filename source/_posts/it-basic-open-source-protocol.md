---
title: IT 基础 —— 开源协议
date: 2019-08-25 22:49:10
tags:
  - 开源
  - 基础
categories:
  - IT 基础
keywords:
  - 泛型
  - Generic
---

![OSI Logo](https://gitee.com/michael_xiang/images/raw/master/osi_keyhole_300X300_90ppi.png)

## 什么是开源

发展史

- 1969 年，贝尔实验室将 Unix 代码共享给社区，为开源奠定了重要基础。
- 1984 年，Richard Stallman 离开 MIT，发起 GUN 项目，希望使用自由代码构建一个类 Unix 的操作系统。此后，Stallman 创立自由软件基金会（FSF），发起 GNU 通用公共协议证书（GNU General Public License, GNU GPL），开发了 GCC、Emacs 等一系列重要产品。
- 1991 年，Linus Torvalds 在 GPL 协议下发布了 Linux 操作系统内核。
- 1995 年，Apache 诞生，随即占据 Web 服务器大部分市场份额。
- 2000 年，开源延伸至移动和云领域，由 Google 等大企业驱动，影响技术发展路线和市场格局。
- 2008 年，Github 等代码托管平台，采用 pull/request 等方式协同合作，将开源推向了新高度。
- 2014 年，Google 开源 Kubernetes，获得极大关注。经过几年发展，Kubernetes 成为事实上的分布式架构平台。

<!-- more -->

## 开源协议

软件的开源协议授予所有用户使用，修改以及共享的权限，并明确表示了哪些行为是准许的，哪些行为是禁止的。不同的开源协议，也有不同的侧重点。

### GPL

我们很熟悉的 Linux 是采用了 GPL。GPL 协议和 BSD、 MIT 等鼓励代码重用的许可很不一样。GPL 要求修改后的代码或者衍生品，也要采用 GPL 协议，既修改后的代码或者衍生品，必须也是开源和免费的。

### BSD

是一个给于使用者很大自由的协议。可以自由的使用，修改源代码，也可以将修改后的代码作为开源或者专有软件再发布。

### MIT

MIT 与 BSD 类似，但是比 BSD 协议更加宽松，是目前最少限制的协议。这个协议唯一的条件就是在修改后的代码或者发行包包含原作者的许可信息。

{% note warning %}
因此，一些公司往往需要小心 GPL（GPL、AGPL、SSPL）的使用
{% endnote %}

开源中国有一篇文章介绍 [MIT 协议](https://mp.weixin.qq.com/s/0dfv2rOLaPhZbSJPd33H6w)，推荐阅读~

## 总结

放两张关于开源协议的总结，非常简洁易懂：

![如何选择开源许可证](https://gitee.com/michael_xiang/images/raw/master/free_software_licenses.png)

上图来自：[阮一峰-如何选择开源许可证？](http://www.ruanyifeng.com/blog/2011/05/how_to_choose_free_software_licenses.html)

![选择一个开源软件协议](https://gitee.com/michael_xiang/images/raw/master/pudhS6.png)

上图来自：[选择一个开源软件协议](http://choosealicense.online/) 对应英文网址[Choose an open source license](https://choosealicense.com)

## 参考

- [开源软件是什么意思？闭源呢？](https://www.kenzhishi.com/1731.html) 一个 UP 主的博客
- [About Open Source Licenses](https://opensource.org/licenses)
- [陈少文-开源正在重构商业模式](https://www.chenshaowen.com/blog/open-source-is-refactoring-the-business-model.html)
- [opensource-logo](https://opensource.org/node/442)
