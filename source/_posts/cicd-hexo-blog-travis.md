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

[Travis CI](https://travis-ci.com) 在 Github 的 Marketplace 中可以看到，这是它 Marketplace 的链接:[Travis CI](https://github.com/marketplace/travis-ci)

`Continuous Integration`，简称 `CI`：意思是，在一个项目中，任何人对代码库的任何改动，都会触发 CI 服务器自动对项目进行构建，自动运行测试，自动编译，甚至自动部署到测试环境。这样做的好处就是，随时发现问题，随时修复。因为修复问题的成本随着时间的推移而增长，越早发现，修复成本越低。

Travis CI 是在线托管的 CI 服务，用 Travis 来进行持续集成，不需要自己搭服务器。

[官网对使用 Travis CI 有详细的使用步骤](https://docs.travis-ci.com/user/tutorial/)：

1. 前往 [Travis-ci.com](https://travis-ci.com/) and Sign up with GitHub.
2. 接受授权
3. 选择你想要使用 Travis CI 的仓库 或者 你也可以在 Github-settings-Applications-TravisCI-Configure 中去更新配置；
4. 在你仓库怎增加 `.travis.yml` 文件，这个文件定义了构建的步骤，例如[安装依赖](https://docs.travis-ci.com/user/job-lifecycle/#customizing-the-installation-phase)等等。
5. 将 `.travis.yml` 文件推送到你的远端仓库，然后就会触发 Travis CI 构建；
6. 登录 [Travis CI](https://travis-ci.com/)然后选择你的仓库查看构建任务的执行详情；

## Job Lifecycle -- Job 生命周期

Travis CI 为每种编程语言提供默认构建环境和默认的阶段集。 创建虚拟机为你的Job提供构建环境，将存储库克隆到其中，安装可选的插件，然后运行构建阶段。

job 的声明周期，主要包含两大部分：

1. `install`：安装依赖，官网有专门讲解的 [Installing Dependencies](https://docs.travis-ci.com/user/installing-dependencies/)
2. `script`：运行构建脚本；

在 `installation` 阶段之前（`beofore_install`）、在 `script phase` 之前（`before_script`）或之后（`after_script`），你可以运行自定义命令；

当构建成功或失败置换后，可以使用 `after_success`（例如构建文档）或 `after_failure`（例如上载日志文件）阶段执行其他操作（actions）。 在 `after_failure` 和 `after_success` 中，您可以使用 `$TRAVIS_TEST_RESULT` 环境变量获取构建结果。

完整的 job 生命周期(包括三个可选的部署阶段，以及在检出 git 存储库 和更改到存储库目录) 如下：

1. [apt addons](https://docs.travis-ci.com/user/installing-dependencies/#installing-packages-with-the-apt-addon) 可选安装
2. [cache components](https://docs.travis-ci.com/user/caching) 可选安装
3. `before_install`
4. `install`
5. `before_script`
6. `script`
7. `before_cache (for cleaning up cache)` 可选
8. `after_success or after_failure`
9. `before_deploy`  可选
10. `deploy`  可选
11. `after_deploy`  可选
12. `after_script`

一次构建任务可有许多 job 组成。

### Install Phase

默认依赖项安装命令取决于项目语言。 例如，Java 构建使用 Maven 或 Gradle，具体取决于存储库中存在的构建文件。

```
install: ./install-dependencies.sh
```

{% note success no-icon %}
When using custom scripts they should be executable (for example, using chmod +x) and contain a valid shebang line such as /usr/bin/env sh, /usr/bin/env ruby, or /usr/bin/env python.
{% endnote %}

你可以选择跳过安装依赖项阶段：

```
install: true
```

### 自定义构建阶段

Ruby 构建示例：

```
script: bundle exec thor build
```

可以定义多脚本命令：

```
script:
- bundle exec rake build
- bundle exec rake builddoc
```

这样的定义，如果第一行命令返回值非 0，并不会影响第二行命令的执行。但是最后的总结果是会标记为 `fail` 。

想要实现那种「串行」效果，可以这么写：

```
script: bundle exec rake build && bundle exec rake builddoc
```

更负载一点的构建命令，就将构建的步骤写到脚本文件中，然后：

```
script: ./scripts/run-tests.sh
```

### 构建中断

下面的四个阶段中，任意阶段中命令抛出非 0，构建都会被中断：

- `before_install`
- `install`
- `before_scrip`
- `script`

{% note warning no-icon %}
如下阶段的退出不会影响构建结果。但是，如果其中一个阶段超时，则构建将标记为失败：

- `after_success`
- `after_failure`
- `after_script`
- `fter_deploy`和
{% endnote %}

### 部署

部署是 job 生命周期中的可选阶段。这个阶段定义了使用持续部署方法去将你的代码部署到 `Heroku`, `Amazon` 或其他支持的平台。如果构建失败，那这一步自然会被跳过的。

将文件去部署时，通过将 `skip_cleanup` 添加到 `.travis.yml` 中，可以阻止Travis CI 重置您的工作目录并删除在构建期间所做的所有更改（类似于这个命令的效果 `git stash --all`）：

```shell
deploy:
  skip_cleanup: true
```

注意点：`before_deploy` 和 `after_deploy` 在执行每个部署任务时，都会被触发执行。

更多内容，查看 [Job Lifecycle](https://docs.travis-ci.com/user/customizing-the-build/#The-Build-Lifecycle)

补充：

[Heroku](https://www.heroku.com/) 是一个支持多种编程语言的云平台即服务。在 2010 年被 Salesforce.com 收购。Heroku作为最开始的云平台之一[1]，从2007年6月起开发，当时它仅支持 Ruby，但后来增加了对 Java、Node.js、Scala、Clojure、Python 以及（未记录在正式文件上）PHP 和 Perl的支持。基础操作系统是 Debian，在最新的堆栈则是基于 Debian 的Ubuntu。

## Build Stages

`build stages` 是一个将 job 分组的方法。在每个 `stage` 中平行的执行 job。但是如果一个 stage 执行失败，会影响到后续的 stage 执行。这不就是 `pipeline` 的模型吗？

在 `stages section` 定义 `stages` 的顺序：

```shell
stages:
  - compile
  - test
  - deploy
```

更多内容，查看 [Build Stages](https://docs.travis-ci.com/user/build-stages/)

## 部署 Hexo 博客

### Travis 准备

为了能够实现代码推送到 Github，需要给 Travis CI Github 的 Persional access tokens，在 [settings- Developer settings](https://github.com/settings/tokens) 可以生成一个。

然后进入 Travis 中的[项目设置界面](https://travis-ci.com/account/repositories)，可以给具体的代码库进行设置，比如增加环境变量：

![](https://ws1.sinaimg.cn/mw690/6d9475f6gy1g435q8jxcsj21p007h3zp.jpg)

主要加了一个环境变量 `GH_TOKEN`，这个在后面的 `.travis.yml` 中会用到。

```shell
# 指定构建环境是Node.js，当前版本是稳定版
anguage: node_js
node_js: stable

env:
 global:
   - URL_REPO: github.com/Michael728/michael728.github.io.git

# 设置钩子只检测blog-source分支的push变动
branches:
  only:
    - hexo

# 设置缓存文件
cache:
  directories:
    - node_modules

#在构建之前安装hexo环境
before_install:
  - npm install -g hexo-cli

#安装git插件和搜索功能插件
install:
  - npm install

  # 设置git提交名，邮箱；替换真实token到_config.yml文件
before_script:
  - git config user.name "Michael728"
  - git config user.email "649168982@qq.com"
  # 替换同目录下的_config.yml文件中github_token字符串为travis后台刚才配置的变量，注>意此处sed命令用了双引号。单引号无效！
  - sed -i "s/github_token/${GH_TOKEN}/g" _config.yml || exit 1

# 执行清缓存，生成网页操作
script:
  - hexo clean
  - hexo generate
  - echo ${ENV_TEST}
  - hexo deploy

# configure notifications (email, IRC, campfire etc)
# please update this section to your needs!
# https://docs.travis-ci.com/user/notifications/
notifications:
  email:
    - 649168982@qq.com
  on_success: change
  on_failure: always
```

这份 yml 文件我做了一些调整，`hexo deploy` 失败，就是会显示失败，而参考文章中有一些写在了 `after_scripts` 中，不方便查看是否部署成功了。

有[文章]([Hexo 自动部署到 Github](https://lotabout.me/2016/Hexo-Auto-Deploy-to-Github/))介绍了可以对 token 进一步加密，然后在 `.travis.yml` 中直接配置密码，这样也可以走通，免走了去配置环境变量的步骤。个人还是偏向去配置环境变量.

- 按照指导，包括[官网的指导-Encryption keys](https://docs.travis-ci.com/user/encryption-keys/)，由于网络太慢，没走通；
- 将密码写在环境中也是比价好的一种方式！

### 代码库准备

之前的博客代码库只有一个 master 分支，存放的都是 `hexo g` 命令生成的静态文件。于是，我需要：

- 本地准备好博客代码库
- 新建一个叫 `hexo` 的分支：`git checkout -b hexo`
- 添加 `.travis.yml` 文件
- 将之前我的博客源文件拷贝到该分之下，并删除`node_modules`、`public`文件夹
- 同时，我也将主题文件下的 `.git` 目录删除了，因为我并需要 submodule 的方式去下载主题仓库了
- 然后将站点配置文件进行一下修改

```shell
deploy:
- type: git
  #repo: git@github.com:Michael728/michael728.github.io.git
  #下方的gh_token会被.travis.yml中sed命令替换
  repo: https://github_token@github.com/Michael728/michael728.github.io.git

  branch: master
- type: baidu_url_submitter
```

说明：`github_token` 这个字段看到了吗？在 `.travis.yml` 文件中，会使用环境变量 `GH_TOKEN` 替换掉它的。因为构建机器上没有配置 ssh 免密，所以需要使用这种 token+http 的方式实现代码的推送

到此基本配置完毕，下载新建文章之后，只需要 `git push origin hexo` 推送到远端分支，在 [Travis CI](https://travis-ci.com/Michael728/michael728.github.io) 中会自动执行部署脚本的。

![](https://ws1.sinaimg.cn/mw690/6d9475f6gy1g436kt051kj21yj0bk76e.jpg)

## 总结

整个过程走下来，感觉和我现在工作中打造的 DevOps 流水线系统很像。开源的这些作品有很多优秀的点值得学习和借鉴，需要去多体验。

昨天在家中折腾搭建好的 Gitlab 和 Gitlab-Runner 应该也可以实现这样的功能，因为它也有一个 `.gitlab-ci.yml` 来定义 CI/CD 流水线。改天有时间，研究一下。

## 参考

- [Hexo+github搭建个人博客（三）：Travis CI持续集成,自动部署博客](http://duansm.top/2018/08/05/hexo-travis/)
- [掘金-Hexo遇上Travis-CI：可能是最通俗易懂的自动发布博客图文教程](https://juejin.im/post/5a1fa30c6fb9a045263b5d2a)
