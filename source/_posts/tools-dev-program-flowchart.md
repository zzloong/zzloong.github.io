---
title: 程序流程图学习笔记
date: 2019-06-01 23:57:10
categories: ToolsDev
tags:
  - 绘图
keywords:
  - 流程图
---

![](https://ws1.sinaimg.cn/large/6d9475f6ly1g3muh769rxj20hs0cqdho.jpg)

最近刚刚加入了部署小组，对接各个部署系统，业务逻辑有点繁杂，这时候想起来之前学过的流程图，或许，它可以帮我顺利整理出各个业务逻辑吧，因此，有了本文。

<!-- more -->

## 概念

流程图（`FlowChart`）是表示算法、工作流或流程的一种框图表示，它以不同类型的框代表不同种类的步骤，每两个步骤之间则以箭头连接。这种表示方法便于说明解决已知问题的方法。流程图在分析、设计、记录及操控许多领域的流程或程序都有广泛应用。

## 符号

> 美国国家标准协会是1960年代就开始制定流程图及一些标准符号[3]。而在1970年，国际标准化组织采用其方案[4]。现时通用的版本ISO 5807是在1985年修订[5]。

所以，流程图的绘制是有标准的，每种符号都有其代表的含义。做事，就要做专业。

![](https://ws1.sinaimg.cn/large/006tNc79ly1g2t56jj4ydj30t70lqgn8.jpg)

![2.jpg](https://i.loli.net/2019/05/07/5cd1a394d4f07.jpg)


## 循环流程图示例

### for 循环

for 循环形式：
```
for (表达式 1；表达式 2；表达式 3){
    执行语句；
}
```

![1.jpg](https://i.loli.net/2019/05/08/5cd2eb7e450a4.jpg)

### while 循环

while 循环形式：
```
while (条件表达式){
    执行语句；
}
```

![2.jpg](https://i.loli.net/2019/05/08/5cd2eb7e4306d.jpg)

### do-while 循环

`do-while` 循环形式：

```shell
do{
    执行语句；
} while(条件表达式)
```

![3.jpg](https://i.loli.net/2019/05/08/5cd2eca45ddb4.jpg)

## 经验

1. 如果你在公司里不是一锤定音式的人物的话，你就需要对你的文档进行版本管理。流程图也不例外，什么时间发布的什么版本，都要清楚地标出来，「最新」是个用不得的词。

## 参考

- [wiki-流程图](https://zh.wikipedia.org/wiki/%E6%B5%81%E7%A8%8B%E5%9B%BE)
- [office-建立基本流程圖](https://support.office.com/zh-tw/article/%E5%BB%BA%E7%AB%8B%E5%9F%BA%E6%9C%AC%E6%B5%81%E7%A8%8B%E5%9C%96-e207d975-4a51-4bfa-a356-eeec314bd276) 这个包含了一些 Visio 制作流程图的技巧
- [edraw-流程图专栏](https://www.edrawsoft.cn/software/flowchart/) 这里介绍了很多技巧
- [丁宇-画Web流程图的一点心得](https://dingyu.me/blog/flowchart-howtos) 该作者博客还总结了一篇英文版的 [The definitive guide to Web flowcharts](https://dingyu.me/blog/the-definitive-guide-to-web-flowcharts)
- [编程基本功训练：流程图画法及练习](https://blog.csdn.net/sxhelijian/article/details/8222858) 介绍了一些比较复杂的例子，借鉴意义
- [【软件工程】看我火眼金睛——系统流程图、程序流程图、数据流图、活动图、状态图、顺序图辨析](https://blog.csdn.net/srk950606/article/details/49637147) 介绍了流程图、时序图、数据流程图等基本概念
- [博客园-大 CC-看懂UML类图和时序图](https://www.cnblogs.com/me115/p/4092632.html)
- [浏览量630.6K，克隆量149.8K的流程图长什么样？](https://mp.weixin.qq.com/s?__biz=MzA4NDM4Nzk3MA==&mid=2653307892&idx=1&sn=508ec62c8067e55dcfe840bd763ac492&chksm=84351219b3429b0f3c6520da3feb8c99fd3a6425268eb5c8755a86849ce8508add4bbad3a988&mpshare=1&scene=1&srcid=&sharer_sharetime=1566475029988&sharer_shareid=ae8eb1508a08c1b134df82bb484ea38d#rd)
