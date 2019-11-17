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

![](https://ws1.sinaimg.cn/large/6d9475f6ly1g225ynphtlj20hs0bt75w.jpg)

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

## [alfred](https://www.alfredapp.com/)

- 唤起快捷键设为：command双击
- 输入框：`lock`，锁屏
- `empty` 清空回收站，瞬间强迫症患者清爽了。
- `eject` 命令可以弹出磁盘、存储卡或者虚拟磁盘镜像，如 .dmg 挂载后的磁盘。
- `open + 文件名`或者`空格 + 文件名` 搜索文件，`Enter` 键直接打开文件，`→`+`Reveal in Finder` 可以在文件夹中打开文件；
- `find + 文件名` 直接在文件夹中打开文件；
- 输入 `in` 命令，以及待搜索的文本，列出磁盘中包含该文本的相关文件，可以快速定位文件，相当于简易的终端 find 命令。

### 快捷搜索URL：

```shell
百度- https://www.baidu.com/s?ie=utf-8&f=8&wd={query}
淘宝 -  https://s.taobao.com/search?q={query}
豆瓣电影：http://movie.douban.com/subject_search?search_text={query}&cat=1002
bilibili：http://search.bilibili.com/all?keyword={query}
百度：https://www.baidu.com/s?ie=utf-8&f=8&wd={query}
stackoverflow：http://www.stackoverflow.com/search?q={query}
githubUser：https://github.com/{query}
githubSearch：https://github.com/search?utf8=%E2%9C%93&q={query}
MDN：https://developer.mozilla.org/zh-CN/search?q={query}
```

参考：
- [掘金-Mac 提升开发效率的小工具](https://juejin.im/post/5b0e99436fb9a009e405dbb6)


### 剪贴板历史

有了 Alfred 的剪贴板增强，以上场景都不是问题，首先要在 Features ->; Clipboard 中打开剪贴板历史：

这里我把它们都设置为存储三个月，同时把打开剪贴板的默认快捷键为 `Ctrl + Shift + C`。

![](https://ws1.sinaimg.cn/large/006tNbRwly1fwfyt1x5kej30u90jgn77.jpg)

通过 `Command + 数字键`，就可以把它粘贴出来

### 文本片段

有一些文本是需要反复输入的，比如你的邮箱、QQ号、地址、手机号码、某个网址等信息，亦或者聊天常用语句、表情等等，这些都可以通过 Alfred 来管理，节省输入时间。打开 Features ->; Snippets：

https://www.alfredapp.com/extras/snippets/

### 字典设置

输入`dc`开头，然后输入查询的单词；

![](https://ws3.sinaimg.cn/large/006tNbRwly1fx3w4qu0acj30uc0jw10l.jpg)

### workflow

个人推荐的workflow:
- [github](https://github.com/gharlan/alfred-github-workflow)：使用之前，需要先`gh > login`。
- ip address：查看本机ip
- 快递查询：输入`kd+单号`
- [YoudaoDict](https://github.com/Rouwanzi/AlfredWorkflow_YoudaoTranslate)
- 人民币金额大写：`cny`然后输入数字即可
- [Hash](https://github.com/bigluck/alfred2-hash)：查看文件MD5值等
- [http_status_code](https://github.com/ilstar/http_status_code):显示 http 状态码含义
- [CodeVar](https://github.com/xudaolong/CodeVar)：
- [encode](https://github.com/willfarrell/alfred-encode-decode-workflow)
- [百度地图](https://github.com/wofeiwo/alfred-baidu-map)

我常用的存储在这儿了：[my-config-files/alfred-work-flow](https://github.com/Michael728/my-config-files/tree/master/alfred-work-flow)

下载源：
- [alfredworkflow](http://www.alfredworkflow.com/)
- [workflow-list](http://www.packal.org/workflow-list)
- [zenorocha/alfred-workflows](https://github.com/zenorocha/alfred-workflows)

### 自定义终端

```shell
on alfred_script(q)
    tell application "iTerm"
        set _length to count window
    if _length = 0 then
        create window with default profile
    end if
    set aa to (get miniaturized of current window)
    if aa then
        set miniaturized of current window to false
    end if
    set bb to (get visible of current window)
    if bb is false then
        set visible of current window to true
    end if
    set cc to frontmost
    if cc is false then
        activate
    end if
        (*if _length = 0 then*)
            set theResult to current tab of current window
        (*else
            set theResult to (create tab with default profile) of current window
        end if*)
        write session of theResult text q
end tell
end alfred_script
```

Alfred使用参考：
- [总是在 Mac 「装机必备」看到的搜索利器 Alfred，究竟是怎么用的？| 新手问号](https://sspai.com/post/43973)
- [效率神器 Alfred workflow 插件推荐](https://hufangyun.com/2018/alfred-workflow-recommend/)
- [Alfred 效率神器全攻略](http://blog.surfacew.com/tool/2016/08/03/Alfred/)
- [Alfred神器使用手册](http://louiszhai.github.io/2018/05/31/alfred/#%E5%A6%82%E4%BD%95%E5%AE%89%E8%A3%85alfred)
- [Mac效率神器Alfred系列教程---文件搜索](https://www.jianshu.com/p/2ce1dd633f4f)

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

PS：我用这款工具的主要原因是，在家里我的 Mac 连接虚拟机的速度非常慢，ssh 到虚拟机执行命令非常卡顿，使用 mosh 之后流畅了许多。

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