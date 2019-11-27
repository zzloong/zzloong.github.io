---
title: Mac 配置教程-日常篇
date: 2018-12-08 15:29:13
tags: [Mac, Tools]
categories: ToolsDaily
keywords:
  - mac
  - config
  - develop
  - app
  - tutorial
---

![](https://ws1.sinaimg.cn/large/6d9475f6ly1fxzdl7bi66j20hs0bu0tc.jpg)

今年终于在推出 2018 款 MBP 时，看到升级了 CPU，我就果断下手「拔草」。本文记录使用 Mac 的一些配置，会长期更新。

为了控制文章的篇幅，我将 Mac 使用配置分成了两篇：

- [Mac 配置教程-日常篇](https://michael728.github.io/2018/12/08/tools-daily-mac/)
- [Mac 配置教程-开发篇](https://michael728.github.io/2019/04/14/tools-dev-mac/)

<!-- more -->

## 系统配置

记录一些 Mac 系统配置方面的技巧，让使用起来更方便。

### 快捷键整理

- `Enter`：选中一个文件 Enter，直接改名
- Ctrl 多用于命令行快捷键，Command 多用于图形化快捷键
- 选中桌面的所有需归档资料，`Command+Sshift+n`，会将文件归档到一个文件夹下
- 调度中心设置：右 `control`，显示桌面

![](https://ws4.sinaimg.cn/large/006tNbRwly1fx3vz0aqxxj30ib0e3ac9.jpg)

- 触发角

![](https://ws3.sinaimg.cn/large/006tNbRwly1fx3w2dz0uxj30ha05n42k.jpg)

- 辅助功能-鼠标与控制板-触控板选项-启用拖移
- `Option+Command+v` 类似剪切的功能，就是粘贴后，原来文件会被剪切过来。复制文件后，除了用快捷键，还可以在文件夹空白处右键，然后再按住 `「⌥」`，原本的「粘贴项目」就变成了「将项目移动到这里」
- 删除文件：
  - `Command+Delete`
  - 工具栏添加垃圾箱，然后选择待删除文件，点击垃圾箱即可删除
- `Command+Option+c` 复制文件路径
- 按住 Option 很多菜单会发生些小变化
- `Control + Command + Space` emoji 表情
- `Command+Up`：回到上一层文件夹
- `Command+Down`：如果是文件夹就进入文件夹，如果是文件就打开
- `Ctrl + Command + q` 锁屏
- 单词，选中，三指，自动翻译单词
- `Command+Shift+.` 临时性切换显示/隐藏
- `Command+Alt+i`: Chrome开发者工具，F12功能
- `command＋shift＋G` Finder中如何输入路径直接跳转，[参考](https://www.cnblogs.com/wormday/archive/2011/05/04/2035984.html)
  - `command+shift+C` 电脑
  - `command+shift+H` 个人
  - `command+shift+D` 桌面
  - `command+shift+A` 程序



### 终端快捷键

- `Ctrl + A`：移动到行首
- `Ctrl + E`：移动到行尾
- `Ctrl + K`：删除到行尾
- `Ctrl + U`：删除到行头
- `Ctrl + N`：移动到下一行
- `Ctrl + P`：移动到上一行

### 设置允许「任何来源」的应用

有时候会遇到这种错误「报错：无法打开已损坏的安装包」，打开终端，键入命令，输入密码，然后回车：

```shell
sudo spctl --master-disable
defaults write com.apple.LaunchServices LSQuarantine -bool false
```

打开「安全性与隐私」，发现久违的「任何来源」回来了。

![](https://ws4.sinaimg.cn/large/006tNbRwly1fxza98jbbyj30id0fkgn2.jpg)

### 改建

为了将外置键盘和Mac键盘的option键、command键保持一致的顺序，做如下修改：
设置-键盘-修饰键-选择键盘（选择外设键盘）-调整顺序

![](https://ws3.sinaimg.cn/large/006tNbRwly1fx3w3kaveuj30bh07g432.jpg)

- [Mac系统切换机械键盘win和alt键](https://www.jianshu.com/p/66922b2bc8c3)

### 使用 Shift 键

Shift 键类似于可视模式，比如在某个位置点击光标并按住 Shift 键不松开，再去另一个位置点击一次，就可以选中两次点击位置之间的文本内容。

### 触控板

- 四指捏合，展开全部应用
- 辅助功能-鼠标 开启三指拖拽

参考：

- [用了那么久 Mac，才知道触控板原来还有这些功能](https://sspai.com/post/44173)

### Mac Split View屏幕分割

- 长按左上角放大的绿色按钮，然后就会选择放置应用的区域！

参考：

- [Mac Split View屏幕分割](https://jingyan.baidu.com/article/a3a3f811eb712d8da2eb8a95.html)

### 更改默认文件打开方式

两指轻按文件，显示简介中，可以看到打开方式选项。

### 电池

- 按住 Option 键并点按菜单栏中的电池图标，以显示电池状态菜单

### Tab 键适用于所有控制

全键盘控制改为「所有控制」

![](https://ws3.sinaimg.cn/large/006tNc79ly1g1wrc2k3asj30ie0frgnj.jpg)

### 其他
- [如何更改 Mac 帐户和个人文件夹的名称](https://mp.weixin.qq.com/s?__biz=MzAxNzcwMTA4Ng==&mid=2247487924&idx=1&sn=af93e65636277a8905a0389cefca2c7f&chksm=9be0d005ac9759134369439e40684ed8b41dfa81bd9a47e82bcd7568b72629dcb15a02df5f4f&mpshare=1&scene=1&srcid=#rd)
- [Mac修改账户名称和个人目录后，进不去系统怎么办？](https://mp.weixin.qq.com/s?__biz=MzAxNzcwMTA4Ng==&mid=2247487924&idx=2&sn=f4af05656734f3743bc2861c56db501b&chksm=9be0d005ac9759134b4d6b103fb9402af27e592fa6c41dbba77491e1fc9d62b0388bb99b262d&mpshare=1&scene=1&srcid=#rd)

## 日常软件

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
# 键盘敲击在屏幕上显示
brew cask install keycastr
# 安装字体
# https://github.com/ryanoasis/nerd-fonts#option-4-homebrew-fonts
brew tap homebrew/cask-fonts
brew cask install font-hack-nerd-font
```

- [git-extras](https://github.com/tj/git-extras/blob/master/Commands.md) 提供了一些有用和有趣的命令，例如 `git summary`
- [程序员内功系列--常用命令行工具](https://xiaozhou.net/learn-the-command-line-tools-md-2018-10-11.html)
- [Brew安装快速预览插件](https://chad-it.github.io/2018/06/26/Brew%E5%AE%89%E8%A3%85%E5%BF%AB%E9%80%9F%E9%A2%84%E8%A7%88%E6%8F%92%E4%BB%B6/)
- [sindresorhus/quick-look-plugins](https://github.com/sindresorhus/quick-look-plugins)

### 分屏软件

目前在用的是一款付费软件 [magnet](http://magnet.crowdcafe.com/#download)，也不贵，6元，感觉还挺好用，下面是它的常用快捷键：

![](https://ws2.sinaimg.cn/large/006tNc79ly1fz4xe7in8kj30au09ajwd.jpg)

[spectacle](https://www.spectacleapp.com/) 这款软件时免费的，也可以体验一下：
- `option+command+←`：窗口左边停靠
- `option+command+→`：窗口右边停靠
- `option+command+f`：窗口全屏

### 视频播放器

- [官宣-IINA播放器](https://lhc70000.github.io/iina/zh-cn/)
- [人人影视客户端](http://app.zimuzu.tv/)

### 截图

- [腾讯-截图](https://jietu.qq.com/)

设定了快捷键为 `Ctrl+Shift+a`

### 下载工具

qBittorrent：
- [qBittorrent](https://www.qbittorrent.org/download.php) 下载
- [搜索插键](https://github.com/qbittorrent/search-plugins/wiki/Unofficial-search-plugins)
- [qBittorrent下载の道](https://zshmobi.com/2018/05/10/qbittorrent-usage/)
- [关于 qBittorrent 连接更多 tracker 的一点补充](https://www.bilibili.com/video/av55388579/)
- [qBittorrent 下载安装和高级设置使用教程 | 添加 trackers 优化下载速度](https://go2think.com/how-to-use-qbittorrent/)

tracker 列表：

- [torrents](https://torrents.io/tracker-list/)
- [ngosang/trackerslist](https://github.com/ngosang/trackerslist)
- [newtrackon](https://newtrackon.com/list)

还有一个下载工具，不过没试用过，做个记录：[Photon](https://github.com/alanzhangzm/Photon/blob/master/README.zh-CN.md)

### 视频制作

- [OBS](https://obsproject.com/) 免费的录屏软件
- [ArcTime](https://arctime.org/download.html) 字幕工具，用来给视频添加字幕，免费。

### 状态栏图标管理

- [Dozer](https://github.com/Mortennn/Dozer) 免费开源，`brew cask install dozer`，足够用了，推荐
- [vanilla](https://matthewpalmer.net/vanilla/) 免费，按住 Command 键，拖动顶部状态栏图标归档之后，就会隐藏图标了。
- [Bartender](https://www.macbartender.com/)：一款非常实用的 Menubar 菜单栏管理工具，付费的

### CatchMouse

Mac双屏时，通过快捷键快速切换到另外一屏。

- [catchmouse下载](http://macappstore.org/catchmouse/)
- [mac下双屏切换](http://shengbinxu.github.io/mac/2015/06/28/mac下双屏切换/)

### cheatsheet

在使用一些软件时，长按 `Command` 按键，会出现快捷键菜单

- [官宣-cheatsheet](https://www.mediaatelier.com/CheatSheet/)

### [Clipy](https://github.com/Clipy/Clipy)

Github 上面开源免费的一款剪贴板软件，简单好用！关键的是还免费，so hacker~

### Downie

Downie：下载各网站视频且更新频繁；

### encrypto

文件加密软件： [encrypto](https://macpaw.com/encrypto) 免费的加密软件

### iMazing

[Mac/Win]备份/管理iOS设备 iTunes 的替代品

### licecap

一款用来制作 Gif 动态图片的小软件：

- [licecap官网](https://www.cockos.com/licecap/)

### manico

默认，长按option，显示dock应用，数字快速切换应用。

### Mos

- [Mos](https://mos.caldis.me/) 鼠标平滑滚动软件，很好用。免费开源

```shell
$ brew cask install mos
$ brew update
$ brew cask reinstall mos
```

### NightOwl

NightOwl ：一款让 Mojave 深色模式更加智能的神器，可以将当前开启的某些软件单独设置成「始终显示为浅色」，这样，即使系统在深色模式下，这些被勾选的软件也会依然显示为浅色界面。

[NightOwl](https://nightowl.kramser.xyz/)

### PopClip

当你在 Mac 上用鼠标选择文字后 PopClip 即会出现。然后即可执行针对内容的特定操作，包括「拷贝」、「复制」以及其它插件操作例如「发送至 Evernote」等。

我安装了扩展：

- `dash` 选中，可以启动 Dash 搜索开发文档
- `terminal`: 复制相关代码时，就可以默认直接去终端执行了，可以设置为默认 iTerm2
- bing
- alfred

这几个扩展，可以到这里下载：[my-config-files/popclip](https://github.com/Michael728/my-config-files/tree/master/popclip)

参考：
- [popclip扩展官网](https://pilotmoon.com/popclip/extensions/)

### Pock

[Pock](https://github.com/pigigaldi/Pock) Mac 的 touchbar 增加一点用途，显示 Dock 的应用图标~推荐！

###  PinPoint
鼠标指针修改

### Pixelmator

图片处理软件

### snap

- [Snap-Mac平台下的快捷键设置App-在Mac下使用机械键盘！](https://zhuanlan.zhihu.com/p/28602277)

### Typora

[Typora](https://typora.io/)：Markdown 编辑器。

### Telegram

- [Telegram使用指南](https://www.jianshu.com/p/9be7ab548e90)

### TinyPNG4Mac
[kyleduo/TinyPNG4Mac](https://github.com/kyleduo/TinyPNG4Mac/blob/master/README_ZH.md) 进行图片压缩的

```
brew cask install tinypng4mac
```

### keycastr

屏幕上显示你敲击的快捷键，或者也可以设置显示你敲击的任意字母。

- [How can I show typing keyboard in record screen](https://apple.stackexchange.com/questions/52618/how-can-i-show-typing-keyboard-in-record-screen)
- [官宣-keycastr/keycastr](https://github.com/keycastr/keycastr)

`brew cask install keycastr`

## Mac 相关好文

- [推荐-Mac OS X 配置指南 | Mac OS X Setup Guide](https://wild-flame.github.io/guides/docs/mac-os-x-setup-guide/guide-introduction)
- [你每天都能用上的 Mac 快捷键](https://sspai.com/topic/145)
- [少数派-macOS 上都有哪些既免费、又实用的工具？| 新手问号](https://sspai.com/post/41477)
- [柠檬精选](https://lemon.qq.com/lab/)