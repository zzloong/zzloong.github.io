---
title: Mac 配置教程-开发篇
date: 2019-04-14 15:05:50
tags: [Mac, Tools]
categories: ToolsDev
keywords:
  - mac
  - config
  - develop
  - app
  - tutorial
---

![](https://gitee.com/michael_xiang/images/raw/master/computer-mac.jpg)

将 Mac 日常使用的软件和开发软件区分开，将之前写的 Mac 配置的文章分成了两篇：

- [Mac 配置教程-日常篇](https://michael728.github.io/2018/12/08/tools-daily-mac/)
- [Mac 配置教程-开发篇](https://michael728.github.io/2019/04/14/tools-dev-mac/)

## 支持 NTFS 磁盘

阅读文章 [Mac 免费支持 NTFS 格式的移动硬盘/U盘](https://michael728.github.io/2019/11/17/tools-mac-support-ntfs/)

## 图床

这篇文章介绍的 [优雅的图床工具，简直完美～](https://mp.weixin.qq.com/s?__biz=MzI5MDM4NTYwOA==&mid=2247488563&idx=1&sn=cb55f805c25b4f2414e6412a1aa2ca58&chksm=ec21ef94db5666825d5a1a3d90dbe52b177688ef5c1241e6b0d4085427bff392eb5f159c9503&mpshare=1&scene=1&srcid=&sharer_sharetime=1564983102216&sharer_shareid=ae8eb1508a08c1b134df82bb484ea38d#rd)

- [uPic](https://gitee.com/gee1k/uPic/releases) 支持利用 Github 或者 Gitee 作为图床，赞了！微博图床封了之后，这个是一个好选择！设置快捷键是 `command+shift+p`，[文档](https://blog.svend.cc/upic/tutorials/gitee/)
- [PicGo](https://github.com/Molunerfinn/PicGo) 设置快捷键是 `command+shift+p`，[文档](https://picgo.github.io/PicGo-Doc/zh/guide/)
- [iPic](https://toolinbox.net/iPic/) 设置快捷键 `Command+Shift+u`

## 免密登录

安装 Homebrew 的指导见 [Mac 配置教程-日常篇](https://michael728.github.io/2018/12/08/tools-daily-mac/)

```shell
brew install ssh-copy-id
ssh-copy-id root@1.2.3.4
# 等价于
ssh-copy-id -i ~/.ssh/id_rsa.pub root@1.2.3.4 -p 22
```

`ssh-copy-id` 这个工具，它的原理其实是把自己的公钥（默认使用 `~/.ssh/id_rsa.pub` 这个文件中的内容）复制到目标服务器的` ~/.ssh/authorized_keys` 文件内。

可以利用 SSH 的配置文件来记住已有的配置，快速登录：

编辑`~/.ssh/config` 文件:

```shell
Host ss
    HostName x.x.x.x
    User root
    Port 22
    IdentityFile ~/.ssh/id_rsa
```

后续，仅需要`ssh ss`，即可免密登录。

- [图解 SSH 原理](https://mp.weixin.qq.com/s?__biz=MzI3MTI2NzkxMA==&mid=2247487942&idx=1&sn=c46f510ff119bd0bdfe30689f0398854&chksm=eac530efddb2b9f9a8b1bf08454084d68b200dc7d4d999fa65d0d1ed69e78c386a26331533ea&mpshare=1&scene=1&srcid=#rd)

## 网络分析

- [HTTP View](https://httptoolkit.tech/view/) 超酷的HTTP(S)的调试助手，一键拦截HTTP(S)，仔细研究和检查流量，并准确地发现您的代码正在发送什么。

## 编程字体

- [Hack](https://github.com/source-foundry/Hack) 专为在源代码中使用而设计的开源字体

## Git

- [下载安装](https://git-scm.com/downloads)

```shell
$ ssh-keygen -t rsa -C "649168982@qq.com"
$ git config --global user.name "Michael728"
$ git config --global user.email "649168982@qq.com"
```

- 我的 Git 配置文件在：[my-config-files/git](https://github.com/Michael728/my-config-files/tree/master/git)
- 更多的设置，阅读 [https://michael728.github.io/2019/11/14/git-install-config/](https://michael728.github.io/2019/11/14/git-install-config/)

### FAQ

Q1： `git status`乱码
执行：`git config --global core.quotepath false`
参考：[Git Status 中文乱码解决](http://blog.crhan.com/2012/09/git-status-%E4%B8%AD%E6%96%87%E4%B9%B1%E7%A0%81%E8%A7%A3%E5%86%B3/)

Q2：Github 下载速度慢

- [加快国内访问Github网站的速度](https://aoenian.github.io/2018/05/12/github-access-fast/)
- [国内为何访问Github如此慢](https://www.wangmaoxian.com/201808/%E5%9B%BD%E5%86%85%E4%B8%BA%E4%BD%95%E8%AE%BF%E9%97%AEGithub%E5%A6%82%E6%AD%A4%E6%85%A2/)

### Homebrew & Cask & launchrocket

在安装其他软件之前，这个首先要介绍安装一下，可以理解为 Mac 平台下的包管理器，用命令行可以安装一下软件工具。

[Homebrew-官网](https://brew.sh/index_zh-cn)安装 brew 命令：

```shell
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

[Homebrew cask](http://caskroom.io/)可以优雅、简单、快速的安装和管理 OS X 图形界面程序，比如 Google Chrome 和 Dropbox，安装 cask 命令：

```shell
brew tap caskroom/cask
```

常用命令：

```shell
brew search xxx // 搜索
brew cask search xxx // 搜索
brew list --versions // 查看安装过的包列表，同时显示版本号
brew update // 更新 brew
brew upgrade <package_name> // 更新用brew安装的软件
brew cleanup // 清理旧版本的包缓存时，清除安装包
brew cask cleanup // 清除安装包
brew doctor // 检测
brew outdated // 看一下哪些软件可以升级
brew info xxx // 查看某个软件信息
brew cask info xxx // 查看某个软件信息
brew unlink vim
brew link macvim
```

更多命令可以通过`man brew`查看：

- [brew 命令官网](https://docs.brew.sh/Manpage)
- [SF-homebrew的tap功能详解](https://segmentfault.com/a/1190000012826983)
- [CSDN-mac的homebrew命令详解&全部选项翻译](https://blog.csdn.net/beyond__devil/article/details/52649362)
- [HelloDog-macOS 使用 Homebrew 的经验分享](https://wsgzao.github.io/post/homebrew/)

Homebrew 能够安装哪些软件：

- `brew search xxx`
- 在[官网](https://formulae.brew.sh/formula/)浏览
- 在[Homebrew formulas index](https://brewformulas.org/)查看详细的使用信息

测试安装是否成功：

```shell
brew install wget
brew cask install google-chrome
brew cask install cakebrew # brew
brew cask install launchrocket # 管理 brew 安装的 service 的工具，安装之后可以看所有的 service 的运行状态
```

- brew 方式安装的软件都在`/usr/local/Cellar`目录下，执行`brew link xxx`，则会在`/usr/local/share`创建软连接。
- brew cask 方式安装的软件在`/usr/local/Caskroom`目录下

Homebrew已成为Mac开发用户必不可少的工具, 大部分开发工具的安装和环境构建都非常方便. 但是如果配合[Cakebrew](https://www.cakebrew.com/)和[LaunchRocket]((https://github.com/jimbojsb/launchrocket), 那将会更方便管理。

[brew和brew cask有什么区别？](https://www.zhihu.com/question/22624898)：[cask](http://caskroom.io/) 更偏向图形化软件的安装。

### Homebrew 源加速

我目前采用的加速方法，可以查看这篇文章 [Mac 环境对 Github Homebrew 等终端工具的加速设置](https://michael728.github.io/2019/11/13/tools-tips-mac-accelate/)

还可以用修改原的方式加速，效果并不好，具体参考：[阿里镜像源-brew](https://opsx.alibaba.com/mirror)

参考：

- [Homebrew有比较快的源（mirror）吗？](https://www.zhihu.com/question/31360766)
- [Homebrew Cask 源使用帮助](http://mirrors.ustc.edu.cn/help/homebrew-cask.git.html)
- [Homebrew专栏](https://sspai.com/topic/181)
- [使用brew cask来安装Mac应用](http://blog.devtang.com/2014/02/26/the-introduction-of-homebrew-and-brewcask/)

### 拓展预览程序

```shell
brew cask install qlcolorcode \
                  qlstephen \ 
                  qlmarkdown \ 
                  quicklook-json \ 
                  qlimagesize \ 
                  qlvideo \ 
                  provisionql \ 
                  quicklookapk \
                  betterzip \
```

- qlcolorcode 预览代码时带有语法高亮
- qlstephen 预览无拓展名的纯文本文件插件
- qlmarkdown
- quicklook-json 预览JSON文件
- qlimagesize 在预览窗口显示图片分辨率及文件大小，并在Finder中显示图片的格式
- qlvideo 让QuickLook兼容.mkv等非原生支持的视频格式，但并不能正常播放，只能显示出一些视频的缩略图和信息
- provisionql 预览iOS / macOS应用和配置信息
- quicklookapk 预览Android APK文件
- betterzip 查看Zip压缩文件的信息以及文件目录

### 安装一些有用的工具（包含开发工具）

```shell
# 免密工具
brew install ssh-copy-id
# 安装 Git 工具
brew install git-extras
# 代码统计工具
brew install cloc
# 连接虚拟机工具
brew install mosh
# 多终端会话软件 tmux
brw install tmux
# 视频下载工具 https://github.com/ytdl-org/youtube-dl
brew install youtube-dl
# 纠正输入的命名 https://github.com/nvbn/thefuck
brew install thefuck
# HTTP 请求工具 https://github.com/jakubroztocil/httpie
brew install httpie
# 全文搜索工具，find 命令对全文搜索有心无力
brew install ack
# MySQL 命令行终端工具，提供语法提示和高亮
brew install mycli 
# 键盘敲击在屏幕上显示
brew cask install keycastr
# 安装字体
# https://github.com/ryanoasis/nerd-fonts#option-4-homebrew-fonts
brew tap homebrew/cask-fonts
brew cask install font-hack-nerd-font
# 安装 docker https://docs.docker.com/docker-for-mac/
brew cask install docker
# 下载工具 qBittorrent增强版 https://github.com/c0re100/qBittorrent-Enhanced-Edition
brew cask install c0re100-qbittorrent
# vscode
brew cask install visual-studio-code
# 终端
brew cask install iTerm2
# wireshark
brew cask install wireshark
```

- [git-extras](https://github.com/tj/git-extras/blob/master/Commands.md) 提供了一些有用和有趣的命令，例如 `git summary`
- [程序员内功系列--常用命令行工具](https://xiaozhou.net/learn-the-command-line-tools-md-2018-10-11.html)
- [Brew安装快速预览插件](https://chad-it.github.io/2018/06/26/Brew%E5%AE%89%E8%A3%85%E5%BF%AB%E9%80%9F%E9%A2%84%E8%A7%88%E6%8F%92%E4%BB%B6/)
- [sindresorhus/quick-look-plugins](https://github.com/sindresorhus/quick-look-plugins)

## 安装 JDK

参考：
- [CSDN-MAC安装JDK及环境变量配置](https://blog.csdn.net/vvv_110/article/details/72897142)
- [Java升级那么快，多个版本如何灵活切换和管理？](https://mp.weixin.qq.com/s?__biz=Mzg3NjIxMjA1Ng==&mid=2247484027&idx=1&sn=38d690c414e3ed299175d85334db08a6&chksm=cf34f84cf843715a29f376bd684cff9ffdf4a6494072f4b3b3dec69e2eff83c96474330b30fb&mpshare=1&scene=1&srcid=&sharer_sharetime=1574228561311&sharer_shareid=ae8eb1508a08c1b134df82bb484ea38d#rd) Jabba jenv sdkman 提供了切换功能

在 Mac 上默认的安装位置在：

```shell
ls -1 /Library/Java/JavaVirtualMachines
```

## Maven

Maven 的安装，可以看我的配置 repo [my-config-files/maven/](https://www.cnblogs.com/helios-fz/p/11061033.html)

## [alfred](https://www.alfredapp.com/)

因为篇幅有限，专门总结了一篇介绍 alfred 的使用：[]()

## [Beyond Compare](https://www.scootersoftware.com/download.php)

Beyond Compare：非常好用的diff工具；

```
cd /Applications/Beyond\ Compare.app/Contents/MacOS/
mv BCompare BCompare.real
vim BCompare
```

添加如下内容：

```
#!/bin/bash 
rm "/Users/$(whoami)/Library/Application Support/Beyond Compare/registry.dat" 
"`dirname "$0"`"/BCompare.real &
```

- 第二行即删除注册信息. 
- 第三行则为启动真实的 BCompare 文件.

参考：
- [CSDN——Beyond Compare for mac 无限试用方法](https://blog.csdn.net/machh/article/details/89707351)
- [诗意——Beyond Compare for Mac 无限试用方法](https://www.seidea.com/2017/03/23/beyond-compare-for-mac-%E6%97%A0%E9%99%90%E8%AF%95%E7%94%A8%E6%96%B9%E6%B3%95/)

## [Charles](https://www.charlesproxy.com/download/latest-release/)

- [Charles 注册码/破解](https://www.jianshu.com/p/89111882fa99)

## Dash

Dash：计算机语言文档神器；
- 选中文本搜索开发文档：`alt/option+s`

## ForkLift 3

Mac 上的 Finder 的增强版本，FTP 工具，官网有[视频介绍](https://binarynights.com/)，强烈安利！

- [被忽视的 FTP 与文件管理工具：ForkLift 3 for Mac](https://sspai.com/post/40554)

## go2shell

从官网下载安装，然后打开，同意安装到Finder。

- [go2shell官网](http://zipzapmac.com/go2shell)
- [Mac通过安装Go2Shell实现“在当前目录打开iTerm2”](https://www.cnblogs.com/shengulong/p/6655558.html)

## iTerm2

- 快捷键打开ITerm2:打开设置，`keys-hotkey-show/hide window`，我设置快捷键为`command+~`
- 切分屏幕：`command+d` 水平切分，`command+Shift+d`垂直切分；`Command + [` 和 `Command + ]` 来切换到左侧或者右侧的标签页。
- 一个标签页中开的窗口太多，有时候会找不到当前的鼠标，`command+/`找到它。
- 按`command+;`弹出自动补齐窗口，列出曾经使用过的命令
- 按`command+Shift+h`弹出历史粘贴记录窗口，`command+Shift+;`弹出历史命令记录窗口。
- `command+d`，新建垂直标签，利用 `command+[/]` 左右切换标签窗口。

```shell
ctrl + w —往回删除一个单词，光标放在最末尾
ctrl + k —往前删除到末尾，光标放在最前面（可以使用ctrl+a）
ctrl + u 删除光标以前的字符
ctrl + k 删除光标以后的字符
ctrl + a 移动光标至的字符头
ctrl + e 移动光标至的字符尾
ctrl + l 清屏
```

iTerm2 中选择文本有三种方式，分别是：

- 双击：选中单词
- 三击：选中整行

参考：

- [你应该知道的 iTerm2 使用方法--MAC终端工具](http://wulfric.me/2015/08/iterm2/)

### 皮肤+字体

- [皮肤资源](https://github.com/mbadolato/iTerm2-Color-Schemes/tree/master/schemes)
- [dracula](https://draculatheme.com/iterm/)：本人采用的就是这个主题，因为VScode中也是它。

字体安装：

```shell
# https://github.com/ryanoasis/nerd-fonts#option-4-homebrew-fonts
brew tap homebrew/cask-fonts
brew cask install font-hack-nerd-font
```

Iterm2 里配置非 acsii 字体。(Preferences -> Profiles -> Text -> Non-Ascii-Font 选择 nerd-font)。之后重启 Iterm2 生效

这套皮肤+字体的设置主要是为了配合zsh终端下的现实效果，zsh的使用配置，见文章：[zsh+on-my-zsh配置教程指南（程序员必备）](http://michael728.github.io/2018/03/11/tools-zsh-tutorial/)

最后效果如下图：

![](https://ws1.sinaimg.cn/mw690/6d9475f6ly1fw7jjv06kdj20j103gwey.jpg)

皮肤安装教程见：

- [iTerm 2 && Oh My Zsh【DIY教程——亲身体验过程】](https://www.jianshu.com/p/7de00c73a2bb)
- [最简单iTerm2 + oh-my-zsh打造Mac炫酷终端教程-菜鸟版实操整理](https://www.jianshu.com/p/53eb1075f627)

### FAQ

Q1: iterm 本地终端中文不乱码，ssh远程中文乱码

- [iterm 配置utf8编码，本地终端中文不乱码，ssh远程中文乱码，咋整啊](iterm 配置utf8编码，本地终端中文不乱码，ssh远程中文乱码，咋整啊)

## Medis

https://github.com/luin/medis

redis 数据库客户端

## MySQL

- [sequelpro](https://www.sequelpro.com/) mysql 客户端，颜值高。免费
- [navicat-premium](http://download3.navicat.com/download/navicat121_premium_en.dmg) 虽然收费，但是真的好用！

navicat 无限制试用，参考
- [NavicatPremium12无限重置试用脚本（MacOS版）](https://zhile.io/2018/08/24/navicat-premium-reset-free-trial.html)
- [Navicat12 无限试用](https://blog.cat73.org/20171219/2017121901.navicat-crack/)
- [Navicat premium 12 for mac 无限试用](https://www.cnblogs.com/blueoceansky/p/10783209.html)

## Node

- [Node官网](https://nodejs.org/en/)：下载LTS版本

```shell
sudo npm install -g hexo # 博客所需
sudo npm install gitbook-cli -g # 笔记所需
```

## Mosh

Mosh 表示移动 Shell(Mobile Shell)，是一个用于从客户端跨互联网连接远程服务器的命令行工具。它能用于 SSH 连接，但是比 Secure Shell 功能更多。它是一个类似于 SSH 而带有更多功能的应用。程序最初由Keith Winstein 编写，用于类 Unix 的操作系统中，发布于 GNU GPL V3 协议下。

Mosh 最大的特点是基于UDP方式传输，支持在服务端创建一个临时的Key供客户端一次性连接，退出后失效；也支持通过SSH的配置进行认证，但数据传输本身还是自身的 UDP 方式。

Mosh 有两个非常有用的功能

- 会话的中断不会导致当前正在前端执行的命令中断，相当于你所有的操作都是在 screen 命令中一样在后台执行。
- 会话在中断过后，不会立刻退出，而是启用一个计时器，当网络恢复后会自动重新连接，同时会延续之前的会话，不会重新开启一个。

Mosh 替换 SSH 链接，需要你本机和远端 host 都安装 mosh 才OK：

```shell
brew install mosh # mac
yum install mosh # centos
apt-get install mosh # ubuntu
```

PS：
- 我用这款工具的主要原因是，在家里我的 Mac 连接虚拟机的速度非常慢，ssh 到虚拟机执行命令非常卡顿，使用 mosh 之后流畅了许多。
- CentOS 有时候还需要有 epel 源才能安装成功 `yum -y install epel-release`

有时候需要服务器上需要打开防火墙的端口：

```shell
sudo firewall-cmd --zone=public --add-port=60001/udp --permanent
sudo firewall-cmd --reload
```

## Python3安装

- [在Mac OS X上安装Python 3](https://pythonguidecn.readthedocs.io/zh/latest/starting/install3/osx.html)
- [Python3-Anaconda](https://www.anaconda.com/download/#macos)

```shell
echo 'export PATH="~/anaconda3/bin:$PATH"' >> ~/.bash_profile
```

安装之后记得重启终端。

## Postman

- [Postman官网](https://www.getpostman.com/)

## Pycharm

- [官宣-Pycharm](https://www.jetbrains.com/pycharm/download/#section=mac)
- [lanyus](http://idea.lanyus.com/) 福利，细心读者能发现 :)

## SourceTree

源码管理：SourceTree

- [sourcetree官网](https://www.sourcetreeapp.com/)

## typora

Markdown 编辑器： [typora官网](https://typora.io/)

## neovim 相关

- [neovim 安装及插键配置](https://michael728.github.io/2019/11/17/tools-vim-plugin-plug/)

## zsh

真是不得不介绍的神器，专门写了一篇文章：

- [zsh+on-my-zsh配置教程指南（程序员必备）](https://michael728.github.io/2018/03/11/tools-zsh-tutorial/)
- [Mac 下环境变量规则及管理](http://shadow000902.space/2018/10/18/Mac-%E4%B8%8B%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E8%A7%84%E5%88%99%E5%8F%8A%E7%AE%A1%E7%90%86/)

## VScode

- [官宣-VS Code](https://code.visualstudio.com/)
- [打造性感好用的VS Code编辑器](https://michael728.github.io/2018/10/28/tools-vscode/)

在 VS code 中快捷键 `Shift + Command + p` 输入 code ,选择安装 code 命令：

![](https://ws1.sinaimg.cn/large/006tNbRwly1fxzcmjc8qhj30gl07a75g.jpg)

然后就可以在终端中这么打开 VS Code了 ：

```shell
code .vimrc
```

## Mac 相关好文

精华其实在最后 :)

- [推荐-Mac OS X 配置指南 | Mac OS X Setup Guide](https://wild-flame.github.io/guides/docs/mac-os-x-setup-guide/guide-introduction) 比较系统的文章
- [Mac开发和编程工具](http://python-web-guide.readthedocs.io/zh/latest/codingtools/codingtools.html)
- [Mac 提升开发效率的小工具](https://juejin.im/post/5b0e99436fb9a009e405dbb6)
- [GitBook-Mac-程序员的自我修养](https://leohxj.gitbooks.io/a-programmer-prepares/software/mac/index.html)
- [serhii-londar/open-source-mac-os-apps](https://github.com/serhii-londar/open-source-mac-os-apps) Github 资源
- [sb2nov/mac-setup](https://github.com/sb2nov/mac-setup) Installing Development environment on macOS [阅读](http://sourabhbajaj.com/mac-setup/) Mac 下各种编程语言开发环境配置指引
- [donnemartin/dev-setup](https://github.com/donnemartin/dev-setup) macOS 开发环境设置

## 最后

原文链接：[Mac 配置总结](https://michael728.github.io/2018/12/08/hobby-mac/)