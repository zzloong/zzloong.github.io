---
title: Github 使用 Travis CI 实现 Hexo 博客自动部署
date: 2019-06-16 14:10:57
categories: DevOps
tags:
    - CICD
    - DevOps
    - Hexo
keywords:
---

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1g432a6exnyj20dc06omx6.jpg)

想学一下 Travis CI 的用法，带着目的学习效果更佳。那么，我最终的目的就是实现 Github 上的[Hexo 博客仓库](https://github.com/Michael728/michael728.github.io)有内容更新后，自动运行 Hexo 部署命更新博客。

<!-- more -->

## Travis CI

[Travis CI](https://travis-ci.com) 在 Github 的 Marketplace 中可以看到，这是它 Marketplace 的链接:

- [Travis CI](https://github.com/marketplace/travis-ci)

`Continuous Integration`，简称CI`CI`：意思是，在一个项目中，任何人对代码库的任何改动，都会触发 CI 服务器自动对项目进行构建，自动运行测试，自动编译，甚至自动部署到测试环境。这样做的好处就是，随时发现问题，随时修复。因为修复问题的成本随着时间的推移而增长，越早发现，修复成本越低。

Travis CI 是在线托管的 CI 服务，用 Travis 来进行持续集成，不需要自己搭服务器。

[官网对使用 Travis CI 有详细的使用步骤](https://docs.travis-ci.com/user/tutorial/)：

1. 前往 [Travis-ci.com](https://travis-ci.com/) and Sign up with GitHub.
2. 接受授权
3. 选择你想要使用 Travis CI 的仓库 或者 你也可以在 Github-settings-Applications-TravisCI-Configure 中去更新配置；
4. 在你仓库怎增加 `.travis.yml` 文件，这个文件定义了构建的步骤，例如[安装依赖](https://docs.travis-ci.com/user/job-lifecycle/#customizing-the-installation-phase)等等。
5. 将 `.travis.yml` 文件推送到你的远端仓库，然后就会触发 Travis CI 构建；
6. 登录 [Travis CI](https://travis-ci.com/)然后选择你的仓库查看构建任务的执行详情；

### Travis 构建语法

https://docs.travis-ci.com/user/customizing-the-build/#The-Build-Lifecycle